## Overview
1. Kubernetes engine을 사용해서 완전한 Kubernetes 클러스터를 프로비저닝합니다.
2. `kubectl`로 Docker 컨테이너를 배포하고 관리합니다.
3. Kubernetes의 디플로이먼트 및 서비스를 사용해서 애플리케이션을 마이크로서비스(Microservice)로 분할합니다.

- `app`이라는 예제 애플리케이션을 사용하여 실습한다.
  - 해당 실습에서는 아래 도커 이미지를 다룹니다.
  1. kelseyhightower/monolith : 모놀리식 + auth 및 hello 서비스 포함
  2. ~/auth : auth 마이크로서비스 + 인증된 사용자를 위한 JWT 토큰 생성
  3. ~/hello : hello 마이크로서비스 + 인증된 사용자를 안내
  4. nginx : auth 및 hello 서비스의 프런트엔드


이 실습에서는 Kubernetes Engine과 같은 관리 환경을 사용하여 기본 인프라를 설정하기보다는 **Kubernetes를 경험하는 데 집중**합니다.


## Google Kubernetes Engine
1. 영역 설정 `gcloud config set compute/zone us-central1-b`
2. 사용할 클러스터 시작   `gcloud container clusters create io`

## 샘플 가져오기
- 깃허브 저장소에서 클론하여 확인


## 간략한 Kubernetes (데모) 시작
- 가장 쉬운 방법 : `kubectl create` 명령어로 nginx 컨테이너의 단일 인스턴스를 실행한다
```
kubectl create deployment nginx --image=nginx:1.10.0
```

Kubernetes가 배포를 생성하면 pod가 작동하며, pod가 실행하는 노드에 오류가 발생해도 계속 작동하게 됩니다. 
Kubernetes에서 모든 컨테이너는 포드에서 실행됩니다.

```
kubectl get pods
// 결과 : 실행 중인 nginx 컨테이너 확인
NAME                    READY   STATUS    RESTARTS   AGE
nginx-56cd7f6b6-gvvsl   1/1     Running   0          35s
```

실행 중인 nginx 컨테이너를 확인하고, Kubernetes 외부로 노출시키자
```
kubectl expose deployment nginx --port 80 --type LoadBalancer
```
- 이제 해당 공개 IP 주소를 조회하는 모든 클라이언트는 서비스 백그라운드에 있는 포드로 라우팅을 합니다.

### 서비스 나열
```
kubectl get services
```

원격으로 Nginx 컨테이너를 조회하려면?
```
curl http://[external-ip]:80

// 결과
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
해당 명령어로 바로 사용할 수 있는 간편한 워크플로 지원 가능합니다.



# 포드(Pods)
Kubernetes의 핵심으로 **포드**가 있습니다.
포드는 **1개 이상의 컨테이너**가 포함된 모음을 의미합니다.
일반적으로 상호 의존성이 높은 컨테이너가 여러개 있으면 이를 하나의 포드에 패키징합니다.

- 즉, 여러개의 컨테이너가 하나의 컨테이너 역할을 하는 단위 개념입니다.

### 포드의 구성
- 논리적 애플리케이션
  - 1개 이상의 컨테이너와 볼륨들
  - 공유되는 namespaces => pod 당 하나의 IP

포드는 콘텐츠에 공유된 네임스페이스를 제공해서, 같은 포드 내에 있는 컨테이너들끼리 서로 통신이 가능하며, <i>볼륨</i>도 공유할 수 있습니다.


> **볼륨**이란?
- 포드가 존재하는 한 계속해서 존재하는 **데이터 디스크**이며, 포드에 포함된 컨테이너에 의해 사용됩니다.

현재 예에서는 모놀리식과 nginx 컨테이너가 포함된 포드입니다.

---

이제 포드에 대해 살펴봅시다.
## 포드 만들기
포드 구성 파일을 사용해서 포드를 만들 수 있습니다.

여기서는 모놀리식 포드 구성 파일을 보겠습니다.

```
apiVersion: v1
kind: Pod
metadata:
  name: monolith
  labels:
    app: monolith
spec:
  containers:               // 현재는 포드가 1개의 컨테이너로 구성
    - name: monolith
      image: kelseyhightower/monolith:1.0.0
      args:                 // 시작할 때 컨테이너로 인수 전달
        - "-http=0.0.0.0:80"
        - "-health=0.0.0.0:81"
        - "-secret=secret"
      ports:
        - name: http
          containerPort: 80 // http 트래픽용 포드 80이 개방
        - name: health
          containerPort: 81
      resources:
        limits:
          cpu: 0.2
          memory: "10Mi"
```

모놀리식 포드를 만듭니다. 도커 허브에서 모놀리식 컨테이너 이미지를 가져옵니다.
```
kubectl create -f pods/monolith.yaml
// 출력
pod/monolith created

// 확인
kubectl get pods

// 출력
NAME                    READY   STATUS    RESTARTS   AGE
monolith                1/1     Running   0          27s
nginx-56cd7f6b6-gvvsl   1/1     Running   0          9m43

// 포드 IP 주소 및 이벤트 로그 등 여러 정보 표시 -> 문제 해결 시 유용하다
kubectl describe pods monolith
```
---

## 포드와 상호작용하기
포드는 기본적으로 비공개 IP 주소가 부여되기 때문에 클러스터 밖에서 접근하려면, 로컬 포트를 모놀리식 포드 내의 포트로 매핑시켜야합니다.
```
kubectl port-forward [포드 이름] 10080:80   // 다른 터미널
// 출력
Forwarding from 127.0.0.1:10080 -> 80

curl http://127.0.0.1:10080      // 다른 터미널에서 pod와 통신 시작(10080으로 접속하면 내부 80으로 매핑)
// 출력
{"message":"Hello"}
```

보안이 설정된 엔드 포인트를 조회하면 어떻게 될까요? 로그인을 하라고 합니다.(비밀번호는 'password')
```
curl http://127.0.0.1:10080/secure  // 로그인 필요
//출력
authorization failed

curl -u user curl http://127.0.0.1:10080/login
// 토큰 출력

```

출력된 JWT 토큰을 환경변수로 만듭시다.

```
TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')
// 토큰을 복사해서 보안이 설정된 엔드포인트를 다시 조회해봅시다
curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure
```

애플리케이션으로부터 모두 잘 동작한다는 응답이 전송되면 포드의 로그를 확인해봅시다.

```
kubectl logs [pod 이름]
// 출력
2022/07/22 03:08:59 Starting server...
2022/07/22 03:08:59 Health service listening on 0.0.0.0:81
2022/07/22 03:08:59 HTTP service listening on 0.0.0.0:80
127.0.0.1:43868 - - [Fri, 22 Jul 2022 03:12:36 UTC] "GET / HTTP/1.1" curl/7.74.0
127.0.0.1:43892 - - [Fri, 22 Jul 2022 03:13:05 UTC] "GET /secure HTTP/1.1" curl/7.74.0
127.0.0.1:43904 - - [Fri, 22 Jul 2022 03:13:26 UTC] "GET /login HTTP/1.1" curl/7.74.0
127.0.0.1:44010 - - [Fri, 22 Jul 2022 03:15:56 UTC] "GET /login HTTP/1.1" curl/7.74.0
127.0.0.1:44064 - - [Fri, 22 Jul 2022 03:17:05 UTC] "GET /login HTTP/1.1" curl/7.74.0
127.0.0.1:44074 - - [Fri, 22 Jul 2022 03:17:21 UTC] "GET /login HTTP/1.1" curl/7.74.0
127.0.0.1:44082 - - [Fri, 22 Jul 2022 03:17:33 UTC] "GET / HTTP/1.1" Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36
127.0.0.1:44112 - - [Fri, 22 Jul 2022 03:18:14 UTC] "GET /secure HTTP/1.1" curl/7.74.0 
```

세번째 터미널에서,
- `-f` 플래스 : 실시간 로그 스트림을 가져올 수 있습니다.
- 다른 터미널에서 curl 요청을 하면, 로그가 실시간으로 쌓이는 것을 볼 수 있습니다.
```
kubectl logs -f [pod 이름] 
```

모놀리식 포드와의 대화형 셸을 실행할 수 있습니다.
```
kubectl exec [pod 이름] --stdin --tty -c [pod이름] /bin/sh

// ping 명령어를 사용해 외부 연결을 테스트해볼 수 있습니다.
ping -c 3 google.com

//출력
PING google.com (142.250.152.113): 56 data bytes
64 bytes from 142.250.152.113: seq=0 ttl=114 time=1.198 ms
64 bytes from 142.250.152.113: seq=1 ttl=114 time=0.565 ms
64 bytes from 142.250.152.113: seq=2 ttl=114 time=0.468 ms
//로그아웃
exit
```

위처럼, 컨테이너를 조회하거나 로그인 셸이 필요한 경우, Kubernetes가 필요한 모든 것을 제공합니다.


# 서비스(Service)
> 언제 필요할까?
- 포드는 영구적으로 지속되지 않습니다. 활성 여부 또는 준비 상태 검사 오류와 같은 다양한 이유로 중지되거나 시작될 수 있기 때문에 문제가 발생할 수 있습니다.

- 그리고 포드가 다시 시작되면, IP주소가 바뀔 수 있습니다. 그럼 포드 집합과 통신을 해야할 때 어떻게 해야할까요?

이 같은 상황에서 서비스가 유용합니다. 서비스는 **포드들을 위해 안정적인 엔드포인트(Endpoint)를 제공**하는 역할을 합니다.

> 서비스의 유형
서비스는 **라벨**을 사용해서 어떤 포드에서 작동할 지 결정합니다.
포드에 라벨이 정확히 지정되어있을 경우, 서비스가 이를 감지하고 노출시킵니다.

서비스가 제공하는 **포드 집합에 대한 엑세스 수준**은 서비스 유형에 따라 다릅니다.
- `ClusterIP`(내부) : 기본 유형, 클러스터 안에서만 볼 수 있습니다.
- `NodePort` : 클러스터의 각 노드에 대해 외부에서 액세스 가능한 IP 주소를 제공합니다.
- `LoadBalancer` : 클라우드 제공업체로부터 부하 분산기(loadBalancer)를 추가해서 서비스에 유입되는 트래픽을 내부에 있는 노드로 전달합니다.


## 서비스 만들기
먼저 https 트래픽을 처리할 수 있는 보안이 설정된 포드를 만듭시다.

모놀리식 서비스 구성 파일을 살펴봅시다.
```
cat pods/secure-monolith.yaml

// 출력
apiVersion: v1
kind: Pod
metadata:
  name: "secure-monolith"
  labels:
    app: monolith
spec:
  containers:
    - name: nginx
      image: "nginx:1.9.14"
      lifecycle:
        preStop:
          exec:
            command: ["/usr/sbin/nginx","-s","quit"]
      volumeMounts:
        - name: "nginx-proxy-conf"
          mountPath: "/etc/nginx/conf.d"
        - name: "tls-certs"
          mountPath: "/etc/tls"
    - name: monolith
      image: "kelseyhightower/monolith:1.0.0"
      ports:
        - name: http
          containerPort: 80
        - name: health
          containerPort: 81
      resources:
        limits:
          cpu: 0.2
          memory: "10Mi"
      livenessProbe:
        httpGet:
          path: /healthz
          port: 81
          scheme: HTTP
        initialDelaySeconds: 5
        periodSeconds: 15
        timeoutSeconds: 5
      readinessProbe:
        httpGet:
          path: /readiness
          port: 81
          scheme: HTTP
        initialDelaySeconds: 5
        timeoutSeconds: 1
  volumes:
    - name: "tls-certs"
      secret:
        secretName: "tls-certs"
    - name: "nginx-proxy-conf"
      configMap:
        name: "nginx-proxy-conf"
        items:
          - key: "proxy.conf"
            path: "proxy.conf"
```

모놀리식 서비스 구성 파일을 봅시다.
```
kind: Service
apiVersion: v1
metadata:
  name: "monolith"
spec:
  selector:
    app: "monolith"     // 라벨이 지정된 포드를 자동으로 찾고
    secure: "enabled"   // 노출시킨다.
  ports:
    - protocol: "TCP"
      port: 443
      targetPort: 443
      nodePort: 31000   // 외부 트래픽을 포트 31000-> 포트 443의 nginx로 전달
  type: NodePort
  ```

위 파일을 이용해 모놀리식 서비스를 만든다.
서비스 노출 시에는 pod가 사용됩니다. 즉, 다른 앱이 서버 중 하나의 포트 31000과 연결을 시도하면 포트 충돌이 발생할 수 있습니다.

`gcloud compute firewall-rules` 명령어를 사용하여 트래픽을 노출된 NodePort의 모놀리식 서비스로 보냅니다.

31000 포트에서 TCP 트래픽을 허용하는 방화벽 규칙을 완성합니다.
```
gcloud compute firewall-rules create allow-monolith-nodeport --allow=tcp:31000
```

## 포드에 라벨 추가하기
- 모놀리식 서비스에 엔드포인트가 없습니다.

```
kubectl get pods -l "app=monolith"
// 출력
NAME              READY   STATUS    RESTARTS   AGE
monolith          1/1     Running   0          25m
secure-monolith   2/2     Running   0          7m31s
```
모놀리식 라벨(`app=monolith`)이 지정되어 실행되는 포드 몇개가 있습니다.

그러나, 'secuire=enabled` 라벨 쿼리를 추가하면, 아무 포드도 출력되지 않습니다.

해당 라벨을 추가해봅시다. 그리고 다시 확인하면, 라벨 2개가 붙어있네요
```
NAME              READY   STATUS    RESTARTS   AGE     LABELS
secure-monolith   2/2     Running   0          9m54s   app=monolith,secure=enabled
```

포드에 정확한 라벨을 지정했으니, 모놀리식 서비스의 엔드포인트 목록을 확인해봅시다.
```
kubectl describe services monolith | grep Endpoints

// 출력
Endpoints:                10.68.2.5:443
```

이제 노드들 중 하나를 조회해서 엔드포인트를 테스트해봅시다.
노드들은 아래 세가지가 조회되네요.
```
$ gcloud compute instances list

NAME: gke-io-default-pool-e69019e6-674f
ZONE: us-central1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.3
EXTERNAL_IP: 34.66.67.234
STATUS: RUNNING

NAME: gke-io-default-pool-e69019e6-mc1c
ZONE: us-central1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.4
EXTERNAL_IP: 35.222.0.215
STATUS: RUNNING

NAME: gke-io-default-pool-e69019e6-nm3j
ZONE: us-central1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.2
EXTERNAL_IP: 35.188.70.155
STATUS: RUNNING
```

성공적으로 "hello" 메시지가 나왔습니다.

# Kubernetes로 애플리케이션 배포하기
목표는 프로덕션의 컨테이너를 확장하고 관리하는 것입니다.
그 때, 디플로이먼트(Deployments)가 유용합니다.

Deployment란?
실행 중인 포드의 개수가 사용자가 명시한 포드 개수와 동일하게 만드는 선언적 방식입니다.

- pod 관리에서 낮은 수준의 세부 정보를 추상화하는 것이 배포(Deployment)의 주요 이점입니다.

> 배포(Deployment) 역할
- 배포는 백그라운드에서 복제본 집합을 사용해서 pod의 시작 및 중지를 관리합니다.
- pod를 업데이트하거나 확장해야하는 경우
- 포드가 중지되면 재시작을 담당

만약, 노드3개가 있을 때, 노드3만 갖고 있던 포드에서 노드3이 중단되면서 포드가 중단되었습니다.

이때 직접 새 포드를 만들고, 이를 위한 노드를 찾는 대신, 디플로이먼트가 새로운 포드를 만들고, Node2에서 실행합니다.
=> 매우 편리한 방식!


이제 디플로이먼트를 사용해서 모놀리식 애플리케이션을 작은 서비스로 분할해봅시다.

# 디플로이먼트(Deployment) 만들기
모놀리식 앱은 세가지 부분으로 나눕니다.
- auth : 인증된 사용자를 위해 JWT 토큰 생성
- hello : 인증된 사용자를 안내
- frontend : 트래픽을 auth 및 hello 서비스로 전달

auth 및 hello 디플로이먼트용 내부 서비스와 frontend 디플로이먼트용 외부 서비스를 정의하겠습니다. 이렇게 하면 모놀리식과 같은 방식으로 마이크로서비스와 상호작용할 수 있으며, 각 서비스를 독립적으로 확장하고 배포할 수 있습니다.

1. auth 디플로이먼트 구성 파일을 검토해봅시다.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth
spec:
  selector:
    matchLabels:
      app: auth
  replicas: 1
  template:
    metadata:
      labels:
        app: auth
        track: stable
    spec:
      containers:
        - name: auth
          image: "kelseyhightower/auth:2.0.0"
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
          resources:
            limits:
              cpu: 0.2
              memory: "10Mi"
          livenessProbe:
            httpGet:
              path: /healthz
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            periodSeconds: 15
            timeoutSeconds: 5
          readinessProbe:
            httpGet:
              path: /readiness
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            timeoutSeconds: 1
```

`kubectl create` 명령어 실행해서 auth 디플로이먼트를 만들면, 디플로이먼트 매니페스트 데이터를 준수하는 포드가 만들어집니다. 즉, 복제본 필드에 명시된 숫자를 변경하여 포드 숫자를 조정할 수 있습니다.


2. 디플로이먼트 개체 생성
```
kubectl create -f deployments/auth.yaml
// auth 디플로이먼트용 서비스 생성
kubectl create -f services/auth.yaml

//위처럼 hello, frontend에 대해서도 진행합니다.
```


## ✋ 정리 ✋
🌟pod와 서비스🌟
``` 
✔️서비스가 있어야 외부에서 pod로 들어갈 수 있다.
✔️서비스는 external ip를 가지고있고, pod의 ip를 알고 있기 때문에 외부와 내부 pod를 연결하는 네트워크 관련된 역할을 한다.
✔️서비스는 pod의 ip를 알고있기 때문에 부하를 골고루 던져준다.


✔️도커는 컨테이너 단위이고, 쿠버네티스는 pod 단위이다.
✔️pod가 여러 개의 컨테이너를 갖는다. 

<service 그림>
✔️서비스가 3개의 replica로 복제 분산된 pod에 트래픽을 부하 분산해준다.

✔️만약 수동으로 컨테이너 관리한다면, pc 한 대가 죽었을 때, 해당 pc위에 있던 pod를 노드1에 올릴지 노드2에 올릴지 결정해야한다.
✔️즉, 명령형으로 pod를 생성했다면 수동으로 pod를 만들어주거나 옮기거나 등을 해야한다. (pod 1개만 띄우는 경우)

✔️그러나 디플로이먼트하는 경우 선언형으로 관리되기 때문에 자동화가 매우 많다. 
(pod는 3개 있어야하고 등등을 선언적으로 설정)
✔️그래서 쿠버네티스가 노드 한대가 죽었을 때 어디에 배포할 지를 내부적인 공식에 의해 우선순위에 따라 배치한다.

✔️디플로이먼트 생성 시, 작성하는 yaml 파일을 보면,
'kind'에 Deployment라고 지정한다. (선언형!)
ex) pod를 삭제하면 자동으로 생성해준다던가..
(그래서 pod가 잘못된 pod라 삭제해도, 계속 생성되기 때문에 deployment를 아예 삭제해야한다.)


✔️디플로이먼트하면 pod와 service가 함께 생성된다.

✔️쿠버네티스의 자동화 : 트래픽이 너무 많아지면 자동으로 스케일 아웃이, 트래픽이 적어지면 스케일 인이 수행된다.
✔️쿠버네티스에서는 pod단위로 ip를 부여하기 때문에 내부 컨테이너는 같은 ip를 공유한다.



🌟포드에 라벨 추가하기🌟
✔️ip로 포드를 찾을 수 없을 때 셀렉터 기능으로, 라벨을 이용해서 찾아서 해당하는 것을 디플로이먼트에게 배당할 수 있다.


🌟configMap이란?🌟
✔️이미지는 불변값이다.
✔️만약 동일한 이미지에서 조금 다른 설정값으로 실행시켜주고자 할 때 configMap을 이용해서 환경변수를 설정하고 pod에 연결을 시키면
pod가 실행될 때 설정한 환경변수를 참고하여 실행한다.

✔️ex) 배포, 개발 환경에 따라 같은 스키마의 db를 다르게 써야할텐데 이미지로 공통되는 db환경을 만들고 환경변수를 적용해서 달리 사용할 수 있다.
```

### Q&A
Q. 왜 node를 생성하지 않고 pod를 생성하는가?<br>
A.쿠버네티스가 node를 생성하는 시간보다 pod 생성 시간이 더 적기 때문이다.