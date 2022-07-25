# Overview 
> 지속적 통합(CI, Continuous Integretion) : 지속적으로 품질 관리를 적용하는 프로세스를 실행하는 것 (빌드 및 테스트 자동화)
> 지속적 배포(CD, Continuous Deployment) : 자동화된 배포를 통해, 개발자의 변경 사항을 리포지토리에서 고객이 사용 가능한 프로덕션 환경까지 릴리스하는 것

- 해당 lab을 통해 컨테이너를 스케일링하고 관리하는 연습을 통해 (일반적으로) 다양한 이기종(heterogeneous) 배포가 이루어지는 시나리오를 수행할 수 있다.

아래의 사항들을 실행해볼 것이다.

1. `kubectl` 툴로 연습 (지속적 배포를 위해 사용하는 툴이다.)
2. 배포(deployment)를 위한 yaml 파일을 생성한다.
3. 배포하고 수정하고 스케일링한다.   참고: [How to scale a deployment](https://www.techrepublic.com/videos/how-to-scale-a-deployment-within-a-kubernetes-cluster/)
4. 배포와 배포 스타일을 수정한다.

# Intro to deployments
이기종(Heterogeneous) 배포는 일반적으로 '특정 기술이나 운영 요구사항을 다루기 위한 서로 구별되는 인프라 환경이나 지역을 연결하는 것을 의미한다.

그래서 이기종 배포는 **배포 특성**에 따라, '하이브리드(hybrid)', '멀티 클라우드(multi-cloud)`, `퍼블릭-프라이빗(public-private)`이라고 불린다.

> 해당 lab의 이기종 배포에는 단일 클라우드 환경, 멀티 클라우드 환경, 온 프레미스와 퍼블릭 클라우드 환경의 조합에서 진행되는 다수의 리전에 걸친 배포를 포함한다.

- 단일 환경이나 지역에 제한된 배포(Deployment)에서는 여러 비즈니스적, 기술적 문제가 발생할 수 있다.
  - 여유 리소스 부족 : 특히 온프레미스 환경에서는 프로덕션 요구를 충족할 컴퓨팅, 네트워킹, 저장소 리소스가 모자랄 수 있다.
  - 제한된 지리적 도달(reach) : 단일 환경에서의 배포는 지리적으로 떨어진 곳에 있는 사용자들이 하나의 배포에 접근해야한다. 이러한 사용자의 트래픽은 특정 위치까지 전 세계를 돌아서 이동한다.
  - 제한된 가용성 : 웹 규모의 트래픽 패턴은 애플리케이션의 내결함성 및 탄력성을 상당히 요구한다.
  - 공급업체(Vendor) 고착화 : 공급업체 수준의 플랫폼과 인프라 추상화는 우리가 애플리케이션을 포팅(이식, porting)하는 것을 힘들게 한다.
  - 유연하지 않은 리소스 : 우리의 자원이 특정 <연산, 저장소, 네트워크 제공(network offering)> 집합으로 제한될 것이다.


이기종 배포는 이러한 문제를 해결하는 데 도움이 될 수 있지만, 프로그래매틱하며 결정론적인 프로세스와 절차를 사용해서 아키텍처를 구성해야 한다. 
그리고 올바른 배포 프로세스는 <i>반복 가능(repeatable)</i>해야하고 프로비저닝, 구성(configuration), 유지를 관리하는 것에 입증된 방식을 사용해야한다.

그리고 이기종 배포를 위한 3개의 일반적인 시나리오가 있습니다.
1. 멀티 클라우드 배포
2. 온프로미스 데이터 프론팅(fronting)
3. CI/CD (지속적 통합/지속적 배포) 프로세스

---

# 실습
## 영역 설정 및 샘플 코드 가져와서 클러스터 생성하기 (노드 5개!)
```
gcloud config set compute/zone us-central1-a

gsutil -m cp -r gs://spls/gsp053/orchestrate-with-kubernetes .
cd orchestrate-with-kubernetes/kubernetes

gcloud container clusters create bootcamp --num-nodes 5 --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
```

## 배포 시작
### 배포 객체(deployment object)를 살펴보자
> 배포 객체는 service, pods들을 생성하는데, (선언형으로 생성할 경우) 자동으로 생성 및 관리해주는 역할을 한다.
- `explain` 명령어 : 배포 객체의 구조와 개별 필드의 기능을 이해하는데 좋은 명령어
- `--recursive` : 모든 필드 확인 옵션.
```
kubectl explain deployment [--recursive]
// 출력
KIND:     Deployment
VERSION:  apps/v1

DESCRIPTION:
     Deployment enables declarative updates for Pods and ReplicaSets.

FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata     <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec <Object>
     Specification of the desired behavior of the Deployment.

   status       <Object>
     Most recently observed status of the Deployment.
```
<br>
배포 객체의 metadata의 name 필드를 알아보고 싶다면?

```
kubectl explain deployment.metadata.name
// 출력
KIND:     Deployment
VERSION:  apps/v1

FIELD:    name <string>

DESCRIPTION:
     Name must be unique within a namespace. Is required when creating
     resources, although some resources may allow a client to request the
     generation of an appropriate name automatically. Name is primarily intended
     for creation idempotence and configuration definition. Cannot be updated.
     More info: http://kubernetes.io/docs/user-guide/identifiers#names
```
<br>

### 배포(deployment)를 만들자
설정 파일을 업데이트한다.
```
vi deployments/auth.yaml

// image를 버전 1.0.0으로 바꾸자.
...
containers:
- name: auth
  image: "kelseyhightower/auth:1.0.0"
...
```

- 배포(Deployment)가 어떻게 하나의 replica를 생성하고, `auth conatiner` 버전 1.0.0을 사용하는지 주목하자.

- `auth` 배포를 생성하면, 배포 매니페스트(Deployment manifest)의 데이터에 따라 하나의 포드(pod)가 생성된다.

- `auth.yaml`의 `replicas` 필드의 숫자를 변경하면 포드 수를 조정할 수 있습니다.


> 매니페스트 파일이란?<br>
> 컴퓨팅에서 집합의 일부 또는 논리정연한 단위인 파일들의 그룹을 위한 메타데이터를 포함하는 파일이다. 예를 들어, 컴퓨터 프로그램의 파일들은 이름, 버전 번호, 라이선스, 프로그램의 구성 파일들을 가질 수 있다.
```
kubectl create -f deployments/auth.yaml
// 배포 생성여부 체크
kubectl get deployments

// 출력
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
auth   0/1     1            0           7s
```

배포가 생성되면, Kubernetes는 배포에 관한 ReplicaSet을 자동으로 만듭니다. 확인!
```
kubectl get replicasets
// 출력
NAME              DESIRED   CURRENT   READY   AGE
auth-85957c88d8   1         1         1       81s

```
배포(Deployment)의 일부로서 생성되는 포드(Pods) 또한 볼 수 있다. 
ReplicaSet이 생성될 때 Kubernetes에 의해 단일 포드가 생성된다.
```
kubectl get pods
// 출력 
NAME                    READY   STATUS    RESTARTS   AGE
auth-85957c88d8-j99pg   1/1     Running   0          103s
```

이제 `auth depoyment`를 위한 서비스(service)를 생성하자.
서비스 메니페스트는 이미 봤었다!
```
kubectl create -f services/auth.yaml
```

auth 배포와 같이 hello Deployment도 만들고 노출(service 생성)하자.
```
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml

// 출력
deployment.apps/hello created
service/hello created

// frontend deployment도 배포 및 노출
// secret/tls-certs와 frontend용 configmap도 함께 생성
kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf   
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
```

프론트엔드의 외부 IP를 가져와서 연결시켜서 상호작용할 수 있다.
```
kubectl get services frontend

curl -ks https://[External-IP]  // hello 메시지를 받아야한다!
```

kubectl의 출력 템플릿 기능을 사용하여 curl을 한 줄 명령어로 사용할 수도 있습니다. //???출력 차이 없음..
```
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`
```

## 배포 확장
생성된 배포를 확장해봅시다. `spec.replicas` 필드를  업데이트하면 됩니다!
```
// spec.replicas 필드에 관한 설명을 먼저 봅시다.
kubectl explain deployment.spec.replicas
// 출력
IND:     Deployment
VERSION:  apps/v1

FIELD:    replicas <integer>

DESCRIPTION:
     Number of desired pods. This is a pointer to distinguish between explicit
     zero and not specified. Defaults to 1.

// 업데이트 명령어 : scale
kubectl scale deployment hello --replicas=5 // ReplicaSet를 3개 -> 자동 업데이트하고, 새로운 pod (5개)를 생성합니다. 
// 출력
deployment.apps/hello scaled
```
배포가 업데이트된 후, Kubernetes는 연결된 ReplicaSet를 자동으로 업데이트하고 **새로운 pod를 시작하여 pod의 총 개수를 5**로 만듭니다.

현재 hello 포드가 5개 실행되고 있는지 확인합니다.
```
// 확인 ('5'라고 뜨네요)
kubectl get pods | grep hello- | wc -l
```

기존에는 deployments 아래 hello.yaml 설정 파일을 보면, 3개로 설정되어있었습니다.

```
// 애플리케이션 다시 축소
kubectl scale deployment hello --replicas=3

// 포드 개수 확인 (3개네요!)
kubectl get pods | grep hello- | wc -l
```
<br>

---


# 순차적 업데이트(Rolling update)
#️⃣ Kubernetes 배포의 순차적 업데이트 및 다운타임 없이 애플리케이션을 업데이트하는 방법

> 배포는 순차적 업데이트 매커니즘(machanism)을 통해 새 버전으로 이미지를 업데이트하는 것을 지원합니다. 배포가 새 버전으로 업데이트 됐을 때, 새 ReplicaSet을 만들고 이전 ReplicaSet에 있는 reaplicas는 감소시키면서 천천히 새 ReplicaSet에 replicas 수를 증가시킨다.

> 관련된 명령어는 `rollout`이다.

<br>

## 순차적 업데이트 트리거(trigger)
```
// hello 배포 편집
kubectl edit deployment hello

// 2.0.0으로 변경 후 저장
...
containers:
  image: kelseyhightower/hello:2.0.0
...
// 출력
deployment.apps/hello edited
```


편집기에서 저장하면 자동으로 업데이트된 배포가 클러스터에 저장되고 Kubernetes에서 순차적 업데이트가 시작됩니다.
```
// Kubernetes에서 생성한 새 ReplicaSet를 확인
kubectl get replicaset

// 출력 (1개 더 추가됐네요!)
hello-6df48465f6      3         3         3       118s

// 출시기록 확인
kubectl rollout history deployment/hello
//출력 ????
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```
<br>

## 순차적 업데이트 일시중지
> 실행 중인 출시에 문제 발생하면 업데이틑 중지합니다.
```
kubectl rollout pause deployment/hello

//출시 상태 확인
kubectl rollout status deployment/hello

// 포드에서 직접 확인하는 방법 (출력 템플릿 설정)
kubectl get pods -o jsonpath --template-'{range .items[*]} {.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```
<br>

## 순차적 업데이트 재개
> 업데이트를 일시중지했기 때문에, 일부 포드는는 새 버전, 일부는 이전 버전이다.
```
kubectl rollout resume deployment/hello

// 출시 완료됐는지 체크
kubectl rollout status deployment/hello
```
## 업데이트 롤백
> 업데이트된 새 버전에서 버그가 발견됐다면 이전 버전으로 롤백하여 문제를 조사한 다음 수정 버전을 출시할 수 있습니다.
```
kubectl rollout undo deployment/hello

//기록 확인 (롤백 확인)
kubectl rollout history deployment/hello

// 모든 포드가 이전 버전으로 롤백됐는지 확인 (2.0.0👉1.0.0)
kubectl get pods -o jsonpath --template-'{range .items[*]} {.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```
---
<br>

# Canary deployments
> **언제 사용할까?**<br>
일부 사용자를 대상으로 프로덕션(production) 환경에서 새 배포에 대해 테스트 하려면, `Canary 배포`를 사용하면 된다.
즉, 새 버전을 만들었는데 옛날 버전과 함께 운영하고 싶을 때 사용하는 배포 방법이다.<br>
👉`Canary 배포`를 사용하면 작은 규모의 일부 사용자에게만 변경 사항을 릴리스하여 <i>새로운 릴리스와 관련된 위험을 완화할 수 있습니다.</i> (즉, 배포별로 사용하는 사용자를 달리할 수 있다.)
<br>

## 1. `Canary Deployments` 만들기
`Canary 배포`는 **새 버전의 별도 배포**와, **기존 안정화된 배포 및 `canary 배포`를 동시에 대상으로 하는 서비스**로 구성된다.

1. 새 버전을 위한 새 `canary 배포`를 생성한다.
```
cat deployments/hello-canary.yaml   // 파일 read -> 버전을 2.0.0
kubectl create -f deployments/hello-canary.yaml

// 이제 `hello`와 `hello-canary` 두개의 deploytments가 있을 것이다. 확인!
kubectl get deployments

// 출력
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
hello          3/3     3            3           16m
hello-canary   0/1     1            0           5s
```

- `hello` 서비스에서는 셀렉터(selector)???가 배포 deployment와 canary deployment 둘다에 포드(pods)를 매칭시킬
`app:hello` 셀렉터를 사용한다.
- 반면 canary deployment는 더 적은 수의 포드를 갖고 있으므로, 그만큼 더 적은 유저에게 보여질 수 있다.<br>
 (일부 사용자에게만 canary deployment가 갖고 있는 포드로 접속하도록 하는 것!)

 ## 2. `Canary Deployments` 검증
 요청에 의해 제공하는 `hello` 버전을 확인하자.
 ```
 curl -ks https://`kubectl get svc frontend -o=jsonpath"{.status.loadBalancer.ingress[0].ip}"`/version

 // 몇번 반복하면 대부분의 요청이 hello 1.0.0으로 제공되고, 일부 집합(1/4=25%)이 2.0.0으로 제공되는 것을 알 수 있다.
 // 실제 5번 요청한 결과, 3번이 1.0.0, 2번이 2.0.0이었다.
 {"version":"2.0.0"}
```

어떤 사용자가 Canary deployment를 통해 서비스받지 못하도록 하려면, 특정 사용자를 특정 배포에 '고정'해야 한다.

<i>서비스와 함께 sessionAffinity 필드로 설정을 하면, ip주소가 동일한 클라이언트에게 항상 동일한 버전의 `hello` 애플리케이션으로 요청을 보낼 수 있다.</i>
```
kind: Service
apiVersion: v1
metadata:
  name: "hello"
spec:
  sessionAffinity: ClientIP
  selector:
    app: "hello"
  ports:
    - protocol: "TCP"
      port: 80
      targetPort: 80
```
---
<br>

# Blue/Green 배포
순차적 업데이트(Rolling updates)는 최소한의 오버헤드(overhead), 최소한의 성능 영향, 최소한의 다운타임으로 애플리케이션을 배포할 수 있기 때문에 가장 좋은 업데이트 방식입니다. <br>
그러나 **배포를 모두 완료한 후에 부하 분산기를 수정하여 새 버전을 가리키도록 하는 것이 유리한 경우**가 있습니다. 이 경우에는 **Blue/Green 배포**가 도움이 됩니다.

> Blue/Green 배포의 주요 단점<br>
애플리케이션을 호스팅하려면 클러스터에 최소 **2배의 리소스가 필요**하다는 점이다.<br>
('Blue' 버전 배포 상태에서 새로운 'Green' 버전 배포를 추가적으로 완료한 후에, 해당 버전을 사용하도록 전환시키기 때문)

## 1. 서비스 업데이트
`hello-blue.yaml` 파일은 기존의 `hello` 서비스에서
app:hello, version: 1.0.0으로 선택기를 업데이트한 것입니다.

셀렉터는 'blue' deployment에 매칭될것이다. ('green' deployment는 다른 버전을 사용하기 때문에 매칭X)
<br>

## 2. Blue-Green Deployment를 사용하도록 업데이트
새 버전으로 'green' deployment를 만들자.
`deployments/hello.yaml` 에서 버전 라벨과 이미지 경로를 수정시킨 것이 `hello-green.yaml`이다.
```
// Green 배포 생성
kubectl create -f deployments/hello-green.yaml

// 현재 Green 배포가 제대로 시작되었는지, 1.0.0 버전이 사용되고 있는지 확인
curl -ks https://`kubectl get svc frontend -o=jsonpath"{.status.loadBalancer.ingress[0].ip}"`/version

//출력
{"version":"1.0.0"}
```

> 잠깐!  ([참고](https://www.lesstif.com/software-architect/curl-http-get-post-rest-api-14745703.html))
- `-k` 옵션 : https 사이트를 SSL 인증서 검증없이 연결하는 옵션 (서버의 정상 동작 여부를 점검할 경우)
- `-s` 옵션 : silent.정숙모드로, 진행 내역이나 메시지 출력하지 않는 옵션
<br><br>

이제 서비스가 새 버전을 가리키도록 업데이트되면, **새 버전의 green 배포**를 사용하게 됩니다.
```
kubectl apply -f services/hello-green.yaml

// 서비스가 업데이트 되면 바로 green 배포가 즉시 사용됩니다. 확인.
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

// 출력
{"version":"2.0.0"}
```

## Blue/Green 롤백
이전 버전(blue 배포)으로 롤백. 즉 blue 배포가 아직 실행 중이라면, 서비스를 이전 버전으로 다시 업데이트하면 green 👉 blue 배포로 업데이트할 수 있습니다.
```
// 서비스를 hello-blue.yaml로 업데이트하여 blue배포로 롤백 성공적!
kubectl apply -f services/hello-blue.yaml
//출력
service/hello configured

// 사용하는 버전 업데이트됐는지 확인!
curl -ks https://`kubectl get svc frontend -o=jsonpath"{.status.loadBalancer.ingress[0].ip}"`/version
// 다시 1.0.0을 가리키므로, 1.0.0버전의 배포로 사용됨
{"version":"1.0.0"}
```

> 다음 실습<br>
Kubernetes 및 다른 인프라 리소스를 사용한 잘 구성된 접근 방법과 함께 이기종 배포를 위한 몇 가지 일반적인 사용 사례를 연습