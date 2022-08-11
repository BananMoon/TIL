## Overview
```
이전 글에서 도커를 다루었고, 이번에는 쿠버네티스를 간단히 다룹니다.
도커는 어떠한 리소스나 환경이 세팅되어있는 이미지를 컨테이너로 생성하여 실행시키는 기술이라면,
쿠버네티스는 이러한 도커(컨테이너)를 관리하는 도구라고 볼 수 있습니다. 여러 개의 컨테이너를 관리하는데에 최적화되어 있습니다. (즉 1개의 컨테이너를 사용 시 쿠버네티스는 필요없겠죠.)
즉, 컨테이너들을 로깅 및 모니터링, 클러스터링 등을 쉽게 할 수 있도록 지원하는 툴입니다.
```


Google Kubernetes Engine(GKE)은 구글 인프라를 이용해서 배포, 관리, 컨테이너화된 애플리케이션을 스케일링하기 위한 관리되는(managed) 환경을 제공한다.

Kubernenetes Engine 환경은 컨테이너 클러스터로부터 그룹화된 다양한 머신기기들로 구성되어있다.

> 클러스터(Cluster)란?
> - 하나의 마스터(Master)와 여러 노드(Node)들이 연결되서 하나의 클러스터라는 개념이 만들어진다.
> - 마스터와 노드는 실제 물리적인 서버이다.
> - 마스터는 쿠버네티스의 메인 기능들을 담당한다.
> - 노드는 실제 App들이 구동되기 위한 자원을 제공하는 역할이다.
> - 만들어진 클러스터 위에 쿠버네티스의 객체(Service, Pod)들을 생성할 수 있다.
> 결국, 애플리케이션 컨테이너를 실행하기 위한 일련의 노드 머신이라고 볼 수 있다.


### GKE로 **컨테이너 생성과 애플리케이션 배포**를 연습해보자.!!
<br>

---

## 클러스터 조정(Cluster orchestration) with GKE
GKE 클러스터는 쿠버네티스 오픈 소스 클러스터 관리 시스템을 기반으로 한다.
쿠버네티스는 우리가 우리의 컨테이너 클러스터와 상호작용할 수 있는 메카니즘을 제공한다.

우리는 쿠버네티스 명령어(commands)와 리소스를 사용해서, 우리의 애플리케이션을 배포 및 관리 업무를 수행하고 정책을 설정하며 배포된 워크로드의 상태(health)를 모니터링할 수 있다.

쿠버네티스는 널리 쓰이는 Google 서비스와 동일한 설계 원칙을 따르고 있어서 자동 관리, 애플리케이션 컨테이너의 모니터링 및 활성 여부 조사, 자동 확정, 순차적 업데이트 같은 이점을 그대로 제공한다.
<br>

---


## 구글 클라우드에서 사용하는 쿠버네티스
GKE 클러스터를 실행하면 Google Cloud의 **고급 클러스터 관리 역량을 활용**할 수 있다는 장점이 있다.
- Compute Engine 인스턴스를 위한 **부하 분산**
- 노드 풀로 클러스터 안에 하위 노드집합을 지정하여 유연성 강화
- 클러스터에서 노드 인스턴스 개수 자동 확장
- 클러스터에서 노드 SW 자동 업그레이드
- 노드 자동 복구 기능 : 노드 상태 및 가용성을 유지 관리
- 로깅 및 모니터링 : 클러스터 현황에 대해 가시성 확보 (Cloud Monitoring 이용)

---
<br>

## 설정 및 요구사항
### 1. 기본 컴퓨팅 영역 설정
- 컴퓨팅 영역(리전 내에 클러스터와 리소스가 존재하는 위치)의 기본 값을 설정하려면, 아래 명령어를 실행한다.  예) `us-central1-a`
```
gcloud config set compute/zone us-central1-a

// 결과
Updated property [compute/zone].
```
### 2. GKE 클러스터 생성
- 클러스터는 1개 이상의 클러스터 **마스터 머신**과 노드라는 다수의 **작업자 머신**으로 구성된다.

```
노드란?
클러스터를 구성하기 위해 필요한 쿠버네티스 프로세스를 실행하는 'Compute Engine 가상머신 인스턴스'이다.
```

> 클러스터 생성 실습
```
gcloud container clusters create [클러스터이름]

// 결과
kubeconfig entry generated for my-cluster.
NAME: my-cluster
LOCATION: us-central1-a
MASTER_VERSION: 1.22.8-gke.202
MASTER_IP: 34.133.72.31
MACHINE_TYPE: e2-medium
NODE_VERSION: 1.22.8-gke.202
NUM_NODES: 3
STATUS: RUNNIN
```

### 3. 클러스터의 사용자 인증 정보 얻기
생성한 클러스터와 상호작용하려면, 사용자 인증 정보가 필요하다.
> 클러스터 인증 실습
```
gcloud container clusters get-credentials [클러스터이름]

//결과
Fetching cluster endpoint and auth data.
kubeconfig entry generated for my-cluster.
```

### 4. 클러스터에 컨테이너식 애플리케이션 배포
이제 클러스터에 <i>컨테이너식 애플리케이션</i>을 배포할 수 있다.

위에서 언급했듯, GKE는 쿠버네티스 객체를 사용해서 클러스터의 리소스를 만들고 관리합니다.
서비스 객체: 인터넷에서 애플리케이션에 엑세스하기 위한 규칙과 부하 분산을 정의합니다.

1. 컨테이너 이미지에서 새 배포(`hello-server`)를 생성한다.
이때 `kubectl create` 명령어를 실행한다.
옵션!
- `--image` : 배포할 컨테이너 이미지 지정 옵션

```
kubectl create deployment hello-server --image=[컨테이너 레지스트리 버킷에서 예시 이미지를 가져왔다]
```

hello-server를 나타내는 **배포 객체**가 생성됩니다.

2. 애플리케이션을 외부 트래픽에 노출할 수 있는 Kubernetes Service를 생성한다.
- 이때 `kubectl expose` 명령어를 실행한다.
- `--port` : 컨테이너가 노출될 포트 지정
- 컨테이너의 Compute Engine 부하 분산기를 생성하기 위해 `--type`을 설정합니다.
```
kubectl expose deployment hello-server --type=LoadBalancer --port 8080

// 결과
service/hello-server exposed
```

`hello-server` 서비스를 검사해봅시다.
```
kubectl get service
// 결과
NAME           TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
hello-server   LoadBalancer   10.104.4.48   34.171.237.94   8080:31852/TCP   50s
kubernetes     ClusterIP      10.104.0.1    <none>          443/TCP          8m50s
```

직접 페이지를 들어가보자. `http://[hello-server의 external-ip]:8080/`


3. 이제 클러스터를 삭제하자.
```
gcloud container clusters delete my-cluster
```
---
<br>

## ✋ 정리 ✋
🌟마스터노드와 워커노드🌟
```
✔️클러스터 : 클라우드에서 서버(컴퓨터)를 모아논 것

마스터와 노드???
✔️쿠버네티스 : 여러 개의 컨테이너를 관리하는 툴


✔️도커 : 하나의 PC 개념


✔️마스터 노드와 워커 노드는 멀티 노드로 구성된다.

✔️서버에 들어가서 명령하지 않고,명령을 내리는 컴퓨터를 별도로 두고 접속한다.

✔️현재 실습에서 google shell에 접속한 것이 하나의 서버이다.
✔️이러한 절차를 쿠버네티스가 수행하게 된다.

✔️쿠버네티스가 원격으로 마스터 노드와 워커 노드를 실행시키는 것인데,
✔️마스터 노드가 명령을 받아서 워커 노드가 수행한다. (컨테이너를 올리고 내린다. 컨테이너가 올라가는 것은 워커 노드에만 올라간다)
✔️현재 명칭 : 마스터 노드 -> 컨트럴플레잉

✔️마스터 노드는 외부에서 명령이 오면 이것을 전달하는 역할을 한다.
예) 컨트럴 플레잉에 'nginx 웹 서버가 필요하다'고 선언하면,
워커 노드에 nginx를 실행하고 있는지 찾아본 후, 없으면 노드를 생성시킨다.

✔️이때 노드가 결함으로 인해 죽으면, 컨트럴 플레잉이 다시 생성시킨다.


✔️추가로,
쿠버네티스도 사실 컨테이너로 되어있다. 
api 명령을 받는 컨테이너, 명령을 받은 것을 db에 저장하는 컨테이너,
각 노드를 실행시키는 컨테이너 등..

```

🌟pod와 service 개념🌟
```
#pod
✔️도커의 단위는 하나의 컨테이너이지만, 
✔️쿠버네티스는 여러개의 컨테이너가 하나의 컨테이너 역할을 하는 pod라는 단위 개념이 있다.
(즉, 여러대의 물리pc가 있지만 가상의 pc1대(pod)로 보는 것이다.)

✔️하나의 pod를 정지시키고자 한다고 선언하면, 쿠버네티스가 그 역할을 하는 pod를 모두 없애버린다.

#Service
✔️pod도 외부에서 접속하려면, 포트포워딩을 해야하는데,
✔️쿠버네티스는 여러개의 pod를 띄우려하기 때문에, 웹서버 pod 2개가 있을 때 접속하려면, pod에 직접 포트포워딩하지 않고, 서비스를 통해 접속한다.
(서비스도 여러 타입이 있다.)

#선언형
✔️쿠버네티스는 선언형인데, 쿠버네티스 입장에서는 새로운 pod를 다시 띄울 땐 노드가 달라질 수 있고, 그럼 IP가 변경이 될 수 있다.

✔️IP가 바뀔 때마다 클라이언트 입장에서 매번 바뀐 IP를 설정하는 것은 힘들다. 그래서 서비스에서 로드 밸런싱하는 역할을 한다.
```