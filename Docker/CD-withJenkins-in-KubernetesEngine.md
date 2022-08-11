
# Overview
해당 실습에서는 Kubernetes 엔진으로 Jenkins를 사용하여 지속적 배포(CD, Continuous Delivery) 파이프라인을 설정하는 방법을 



## Kubernetes Engine이란 무엇인가?
- **컨테이너를 위한 강력한 클러스터 관리자 및 조정 시스템인 Kubernetes**의 Google Cloud 호스팅 버전
- Kubernetes란?
  - 다양한 환경에서 실행할 수 있는 오픈소스 프로젝트
  - 컨테이너로 구축되며 실행을 위해 필요한 모든 종속성과 라이브러리와 함께 번들로 제공되는 경량 애플리케이션
  - 기본 구조 덕분에 고가용성과 안정성을 갖추고 빠른 배포가 가능하여 클라우드 개발자에게 이상적인 프레임워크이다.

- Kubernetes Engine은 일시적 빌드 실행자(ephemeral build executors)를 제공한다.
  - 이점 1. 빌드가 활발하게 실행될 때만 사용되므로, 일괄 처리 작업과 같은 다른 클러스터 작업에 사용할 여유 리소스를 확보할 수 있다. 
  - 이점 2. 속도. 시작하는 데 몇 초밖에 걸리지 않습니다.

- Kubernetes Engine에는 Google의 전역 부하 분산기가 사전 설치되어 있어 인스턴스로의 웹 트래픽 라우팅을 자동화하는 데 사용할 수 있다.

> 부하 분산기<br>
> 부하 분산기는 SSL 종료를 처리하고, 웹 프런트와 결합되어 Google 백본 네트워크로 구성된 전역 IP 주소를 활용합니다. 이 부하 분산기는 사용자가 항상 애플리케이션 인스턴스에 이르는 가장 빠른 경로로 액세스할 수 있도록 설정해 줍니다.


## Jenkins란?
- 빌드, 테스트, 배포 파이프라인을 유연하게 구성할 수 있는 오픈소스 자동화 서버
- 코드를 공유 저장소에서 통합하는 개발자가 가장 많이 사용하는 자동화 서버
- Jenkins를 사용하는 개발자는 <i>지속적 배포</i>로 인한 오버헤드 문제를 걱정할 필요 없이 프로젝트를 신속하게 변경 및 개선할 수 있다.

지속적 배포(continuous delivery, CD) 파이프라인을 설정해야 하는 경우,
Jenkins를 Kubernetes Engine으로 배포하면 표준 VM 기반 배포 대비 상당한 이점을 얻을 수 있다.

빌드 프로세스에서 컨테이너를 사용하는 경우, 하나의 가상 호스트로 여러 운영 체제에서 작업이 가능하다.
<br>
---

## 1. Jenkenis 프로비저닝하기
Kubernetes 클러스터(이름: jenkins-cd)를 프로비저닝합니다
```
gcloud container clusters create jenkins-cd \
--num-nodes 2 \
--machine-type n1-standard-2 \
--scopes "https://www.googleapis.com/auth/source.read_write,cloud-platform"
// scopes 매개변수는 Jenkins가 Cloud Source Repositories 및 Google Container Registry에 액세스할 수 있도록 한다.


//클러스터가 실행 중인지 확인
gcloud container clusters list

// 출력
NAME: jenkins-cd
LOCATION: us-east1-d
MASTER_VERSION: 1.22.8-gke.202
MASTER_IP: 35.243.208.131
MACHINE_TYPE: n1-standard-2
NODE_VERSION: 1.22.8-gke.202
NUM_NODES: 2
```

내 클러스터(jenkins-cd)의 사용자 인증 정보를 가져와서,  새로 프로비저닝된 클러스터에 액세스해야하는데, 연결이 가능한지 체크하자.
```
gcloud container clusters get-credentials jenkis-cd
//출력
kubeconfig entry generated for jenkins-cd.

// 연결 가능 체크
kubectl cluster-info
// 출력
Kubernetes control plane is running at https://35.243.208.131
GLBCDefaultBackend is running at https://35.243.208.131/api/v1/namespaces/kube-system/services/default-http-backend:http/proxy
KubeDNS is running at https://35.243.208.131/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://35.243.208.131/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
```

## 2. Helm 설정
Helm을 사용하여 차트 저장소에서 Jenkins를 설치한다.

> Helm?<br>
> Helm은 Kubernetes 애플리케이션을 쉽게 구성하고 배포할 수 있는 패키지 관리자

```
// helm의 안정적인 차트 레포를 추가합니다.
helm repo add jenkins https://charts.jenkins.io
//출력
"jenkins" has been added to your repositories
```

Jenkins를 설치하면, CI/CD 파이프라인을 설정할 수 있습니다.

## 3. Jenkins 구성 및 설치
Jenkins 설치 시, `values` 파일을 템플릿으로 사용해서 설정에 필요한 값을 제공할 수있다.
사용자 지정 `values` 파일을 사용하여 Kubernetes 클라우드를 자동으로 구성하고 다음 필수 플러그인을 추가하자.
- Kubernetes:1.29.4
- Workflow-multibranch:latest
- Git:4.7.1
- Configuration-as-code:1.51
- Google-oauth-plugin:latest
- Google-source-plugin:latest
- Google-storage-plugin:latest

이렇게 하면 Jenkins가 클러스터와 GCP 프로젝트에 연결할 수 있다.

1) values 파일 다운로드!
```
gsutil cp gs://spls/gsp330/values.yaml jenkins/values.yaml
```

2) Helm CLI를 사용해서 현재 구성한 설정(`values.yaml`)으로 차트를 배포하자.
```
helm install cd jenkins/jenkins -f jenkins/values.yaml --wait

//Jenkins 포드가 Running 상태이고 컨테이너가 READY 상태인지 확인
kubectl get pods
// 출력
NAME           READY   STATUS    RESTARTS   AGE
cd-jenkins-0   2/2     Running   0          117s
```
3) 클러스터에 deploy하기 위해 Jenkins 서비스 계정을 설정하자.
```
kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins
//출력
clusterrolebinding.rbac.authorization.k8s.io/jenkins-deploy created
```

4) 클라우드 쉘에서 Jenkins UI에 포트포워딩을 세팅하자.
```
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

// Jenkis 서비스가 올바르게 생성되었는지 확인
kubectl get svc
//출력
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE
cd-jenkins         ClusterIP   10.100.9.249   <none>        8080/TCP    4m5s
cd-jenkins-agent   ClusterIP   10.100.4.61    <none>        50000/TCP   4m5s
kubernetes         ClusterIP   10.100.0.1     <none>        443/TCP     13m
```

Jenkins 마스터가 요청할 때 필요에 따라 빌더 노드가 자동으로 시작되도록 Kubernetes 플러그인을 사용하고 있습니다. 관련 작업이 완료되면 자동으로 해제되고 리소스가 클러스터 리소스 풀에 다시 추가됩니다.

이 서비스는 selector와 일치하는 모든 포드에 관해 포트 8080 및 50000을 노출합니다. 그러면 Kubernetes 클러스터 내의 빌더/에이전트 등록 포트와 Jenkins 웹 UI가 노출됩니다. 또한 jenkins-ui 서비스는 클러스터 외부에서 액세스할 수 없도록 ClusterIP를 사용하여 노출됩니다.

## Jenkins에 연결하기

Jenkis 차트가 자동 생성한 관리자 비밀번호를 검색하자.
이를 이용해 Jenkins에 로그인할 수 있습니다. 

## 정리
```
<git action>
: merge되면 jenkins가 실행되도록 함. jenkins가 git에서 파일을 읽도록 연동하는 것.