> 이전 글에서 말했듯이 웹브라우저를 띄울 때 방법은 두가지가 있다.
> 1. HTML로 응답하기 (구체적으로는, HTML+CSS+JavaScript)
> 2. 데이터만 띄우기

<br>

이번에는 2번째 방식을 진행해볼 것이다. <br>
$ 요청에 대해 **데이터**를 응답하기 위해서는 `RestController`를 사용해야한다.

> Rest?<br>
> 서버의 응답이 JSON 형식(데이터)임을 나타냅니다. HTML, CSS 등을 주고받을 때는 Rest를 붙이지 않는다!!

> Controller?<br>
> 자동 응답기! 누군가 요청(Request)하면 응답하는 녀석! 즉, 요청을 전달받는 코드이다.

> RestContorller? 
> - JSON형식으로 응답하는 자동 응답기!
> - RestController 역할을 하는 빵틀을 먼저 만들어보자! 즉, 클래스를 생성해보자.

<br>

### § 우리가 이전에 생성한 멤버변수 title, tutor, days로 구성된 하나의 데이터를 JSON형식으로 가져오도록 요청해본다.

- controller를 위한 패키지를 생성하고, 그 아래 클래스를 만들어본다.
```java
package com.moonz.springPractice01.controller;

import com.moonz.springPractice01.javaPrac.Course;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
// 응답에 최적화된 빵틀 생성!
//메서드 호출? Spring이!
@RestController
public class CourseController {

    @GetMapping("/course")
    public Course getCourses() {
        Course course = new Course();
        course.setTitle("웹개발의 봄 스프링");
        course.setDays(35);
        course.setTutor("moonz");
        return course;
    }
    // 이렇게 객체를 반환만 하면 JSON으로 변환해서 브라우저에 응답하는 것? Spring이 다해준다!
}
```

### $ 그레들(Gradle)이란?
> 다른 사람이 만들어놓은 도구를 이용하는 것은 (과하지 않은 선에서) 훌륭한 능력이 될 수 있다.

  - 다른 사람이 만들어둔 도구를 내려받는 방법은 언어마다 다른데, 이를 통해 다운로드 및 적용하는 과정이 보다 편리해진다.
    - Javascript - NPM
    - Python - pip
    - Java - mavenCentral, jcenter

- gradle은 위의 역할(라이브러리 적용) 뿐만 아니라 **프로젝트를 배포**하는데에도 쓰인다.

<br>

### $ 라이브러리 다운 및 적용
- 다른사람들이 만들어놓은 라이브러리를 검색하기 위해 [여기](https://mvnrepository.com/)에 접속한다.
- 우리는 Java에서 Json과 호환될 수 있도록 **JSON in Java** 라는 라이브러리를 다운받을 것이다. ([여기](https://mvnrepository.com/artifact/org.json/json/20160810)가 가장 사용자가 많았다.)
- 우리는 Gradle을 사용한다 했었으니, Gradle에 해당하는 코드를 복사한 후, <br>build.gradle 파일의 `dependencies` 부분에 붙여주고 `dependencies`를 한번 실행을 시키고나면, 성공적으로 빌드된다.
