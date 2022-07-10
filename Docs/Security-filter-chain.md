### 개요
스프링 시큐리티(Spring Security)의 웹 인프라는 표준 서블릿 필터(servlet filters)를 기반으로 한다. 즉, 다른 프레임워크(Spring MVC)를 기반으로 하는 서블릿을 사용하지 않는다는 의미이다.
그래서, 내부적으로 특정 웹 기술과 강하게 연결되어 있지 않다. 그렇기 떄문에 `HttpServletRequests`나 `HttpServletResponse`를 다루기만 할뿐, 요청이 어떤 브라우저, 웹 서비스 클라이언트, HttpInvoker, AJAX 애플리케이션에서 왔는지 신경쓰지 않을 수 있다.

스프링 시큐리티는 내부적으로 필터체인(filter chain)을 관리하는데, 각각의 필터들은 아래 특징이 있다.
- 특정 책임을 갖고 있다.
- 서비스의 필요에 따른 설정으로부터 추가되거나 제거될 수 있다.
- 필터들 사이의 의존 관계가 있기 때문에 필터들의 순서는 매우 중요하다.
만약 [namespace 설정](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/ns-config.html)을 했다면, 필터들은 자동적으로 그에 맞게 설정이되고, 따로 스프링 빈(Spring beans)을 명시적으로 정의할 필요가 없다.
- 하지만, 
우리가 시큐리티 필터체인에 대한 전체 권한을 원한다면 (nampespace에서 지원되지 않는 거나 커스터마이즈된 클래스의 버전을 사용하길 원하는 경우) 직접 정의하는 경우도 있다.

**DelegatingFilterProxy** hmm..
사실 `DelegatingFilterProxy`는 실제로 필터의 어떤 로직을 구현하는 클래스는 아니지만, 필터의 메서드들을 스프링 애플리케이션 컨텍스트(context)에서 얻은 빈(bean)에 위임(delegate)하는 역할을 한다.
    - 이것은 빈이 스프링 웹 애플리케이션 컨텍스트 라이프 사이클 지원과 설정의 유연성의 이점을 누리도록 한다.
빈은 `javax.servlet.Filter`를 구현해야하고 `filter-name` 요소 이름과 동일해야한다.

**FilterChainProxy**  hmm..
- 스프링 시큐리티의 웹 인프라는 `FilterChainProxy`의 인스턴스에 위임하여 사용해야 한다.
- 시큐리티 필터는 단독으로 사용되어서는 안된다.
  - 이론적으로 애플리케이션 컨텍스트 파일에 필요한 각 스프링 시큐리티 필터 빈을 선언하고 '너는 너의 애플리케이션 컨텍스트 파일에 필요하고 각 필터에 대해 해당 `DelegatingFilterProxy` 항목을 추가할 수 있다.
  - 그 필터들이 올바르게 정렬되었는지 확인하지만 필터가 많으면 매우 번거롭고 복잡해진다는 것을 명심해야한다.
- `FilterChainProxy`를 사용하면, namespace에 단일 엔트리를 추가하고, 완전히 우리의 웹 시큐리티 빈을 관리하기위해 애플리케이션 컨텍스트 파일을 완전히 처리할 수 있다.
- 해당 필터 또한 `DelegatingFilterProxy`를 사용해서 연결되지만, 필터 이름(빈 이름)은 `filterChainProxy`로 설정된다.
- 이 필터 체인은 동일한 빈 이름으로 애플리케이션 컨텍스트에 선언된다.

## Filter Ordering
- 체인(chain)에서 정의되는 필터의 순서는 매우 중요한데, 실제로 사용하는 필터에 관계없이 순서는 아래와 같아야 한다.
1. `ChannelProcessingFilter` : 다른 프로토콜로 리다이렉트(redirect)하기 위해 필요
2. `SecurityContextPersistenceFilter` : 시큐리티 컨텍스트(SecurityContext)는 웹 요청의 시작 시점에 `SecuriyCOntextHolder`에 셋업(set up)이 되고, 시큐리티 컨텍스트에 변화가 있을 때는 웹 요청이 끝난 후 `HttpSession`으로 복사된다.
3. `ConcurrentSessionFilter` : `SecuityContextHolder` 기능을 사용하지만 보안 주체(principal)의 지속적인 요청을 반영하기 위해 `SessionRegistry`를 업데이트 해야한다.

4. 인증 (Authentication) 절차 메카니즘
- `UsernamePasswordAuthenticationFilter`
- `CasAuthenticationFilter`
- `BasicAuthenticationFilter` 등..
- 유효한 인증 요청 token을 포함하도록`SecuityContextHolder`가 수정될 수 있다.

5. `SecurityContextHolderAwareRequestFilter` : 만약 스프링 시큐리티를 인식하는 `HttpServletRequestWrapper`를 서블릿 컨테이너에 설치하는데 사용하는 경우

6. RememberMeAuthenticationFilter` : 