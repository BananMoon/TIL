# 🚩 Github Actions 문서로 이해하기 
## Overview 😎
지속적인 통합(continuous integeration)과 지속적 전달(continuous delivery) 플랫폼
- 사용자가 파이프라인✋을 빌드하고, 테스트, 배포하는 것을 자동화하도록 해주는 Ci/CD 플랫폼
- 레포지토리의 PR을 빌드하고 테스트해주는 워크플로우를 만들거나 프로덕션에 머지된 PR을 배포(deploy)할 수 있다.
  - 예) 누군가 새 이슈(issue) 등록했을 때 자동으로 적절한 라벨을 추가해주는 워크플로우

- 워크플로우를 실행시킬 가상머신으로 Linux, Windows, macOS를 제공한다.

> ✋ 파이프라인?<br>
>  한 데이터 처리 단계의 출력이 다음 단계의 입력으로 이어지는 형태로 연결된 구조를 가리킨다. 이렇게 연결된 데이터 처리 단계는 한 여러 단계가 서로 동시에, 또는 병렬적으로 수행될 수 있어 효율성의 향상을 꾀할 수 있다. 


<br>

---
## Github Actions의 구성(components) 🥳
- 당신의 레포지토리에서 이벤트(event)가 발생했을 때 트리거되는 워크플로우를 구성할 수 있다.
  - 예) 오픈된 PR, 생성된 이슈
- 워크플로우는 하나 이상의 작업(jobs)을 포함할 수 있다. (in parallel)
- 각 작업은 그것만의 가상 머신 runner 내부에서 혹은 컨테이너 내부에서 동작한다.
- 사용자의 워크플로우를 단순화하는 재사용 가능한 액션(action)이나 사용자가 정의한 스크립트를 실행하는 여러 단계를 갖을 수 있다.

<br>

---
### 💭 이제 GitHub Actions의 구성요소들을 하나씩 알아보자.

## Workflows 🤓
- 하나 이상의 작업(jobs)을 수행할, 설정할 수 있는 자동화된 절차(process)이다.
- 워크플로우는 사용자의 깃허브 레포지토리에 있는 YAML 파일로 정의된다.
  - `.github/workflows` 디렉토리 에 정의되는데, 다수의 워크플로우를 갖을 수 있고, 각각의 워크플로우들이 서로 다른 작업 세트들을 수행한다.
  - 예) PR을 빌드하고 테스트하는 워크플로우와 릴리즈(release)가 생성될 때마다 애플리케이션을 배포하는 워크플로우, 그리고 누군가 새 이슈(issue)를 오픈할 때마다 라벨을 추가하는 워크플로우가 있다.
- 다른 워크플로우 내에 워크플로우를 참조할 수도 있다.
- 사용자의 레포지토리 내 이벤트(event)에 의해 트리거 되거나 수동으로 트리거되거나 특정 일정에 트리거될 것이다.

<br>

---
## Events 🤤
- 워크플로우가 작동하도록 트리거하는 레포지토리 내 **특정 액티비티(activity)** 이다.
- 예) 누군가 PR을 생성 or 이슈(issue)를 오픈 or 레포지토리에 커밋(commit)을 푸쉬(push) 할 때 액티비티가 Github에서 시작(originate)된다. 
- 일정에 따라 워크플로우가 실행하도록 트리거할 수 있다.

<br>

---
## Jobs 😳
- 같은 runner에서 수행하는 **워크플로우 안에 여러 단계의 세트**이다.
- 각각의 스텝(steps)은 수행될 쉘 스크립트이고 수행될 액션(action)이다.
- 각 스텝은 순서대로 실행되고, 서로 종속적(dependant)이다.
- 각 스텝은 같은 runner에서 수행되므로, 스텝 간 데이터를 공유할 수 있다.
- 예) 애플리케이션을 빌드하는 단계 후에 빌드된 애플리테이션을 테스트하는 단계를 갖을 수 있다.

- 다른 작업들(jobs)와 의존성을 맺을 수 있다. (기본적으로, 작업들간에는 종속성이 없고 병렬로 실행된다.)
  - 의존하는 다른 작업들의 수행이 완료될 때까지 기다린 다음에 수행한다.
  - 예) 종속성 없는 서로 다른 아키텍처(architectures)에 대해 여러 빌드 작업들(build jobs)에 종속된 패키징 작업(packaging job)이 있다면, <br>빌드 작업들은 병렬적으로 수행되고, 해당 작업을 성공적으로 마치면 패키징 작업이 수행될 것이다.

<br>

---
## Actions 😵
- 복잡하지만 자주 반복되는 작업(task)를 수행하는 GitHub Actions 플랫폼용 **커스텀 애플리케이션**이다.
- 워크플로우 파일에서 우리가 작성한 것중 반복되는 코드의 양을 줄이는데에 도움이 된다.
- GitHub에서 레포지토리를 가져오거나(pull) 빌드 환경에 맞는 올바른 도구체인(toolchain)을 셋업(set up)하거나, <br>우리의 클라우드 공급자(aws,..)에 대한 인증을 셋업할 수 있다.
- 사용자만의 actions를 작성할 수도, GitHub Marketplace에서 사용자의 워크플로우에서 사용할 actions를 찾을 수 있다.

<br>

---
## Runners 🧐
- 우리의 워크플로우가 트리거될 때 수행(run)하는 **서버**이다.
- 각각의 러너(runner)는 동시에 단일 작업을 수행할 수 있다.
- GitHub는 우리의 워크플로우를 수행할 러너로 Ubuntu Linux, Microsoft Windows, macOS 러너를 제공한다.
- 각 워크플로우 실행은 새로 프로비저닝된 가상 머신(VM)에서 실행된다.
- 만약 사용자가 다른 OS나 특정 HW 설정을 요구한다면, <br>사용자만의 러너(runner)를 호스팅할 수 있다. (`self-hosted runners`)

<br>

---
### 💭 간단한 워크플로우를 설정하는 실습을 보자.

## Examples 😲
1. 워크플로우 파일을 저장할 디렉토리`.github/workflows/`를 생성한다.
2. `learn-github-actions.yml` 파일을 생성하고 코드를 작성한다.
```yml
name: learn-github-actions   # Actions 탭에 나타날 워크플로우 이름
on: [push]  # 워크플로우의 트리거 구체화. 푸쉬 이벤트(push, merge)
jobs:   # 해당 워크플로우에서 실행되는 모든 작업들 그룹화
  check-bats-version:   # jobs명 (하위 키들은 jobs의 속성을 정의)
    runs-on: ubuntu-latest  # Ubuntu Linux runner의 최신 버전에서 작동
    steps:  # 해당 job에서 수행하는 스탭들 그룹화
      - uses: actions/checkout@v3   # uses: 실행할 작업 지정. 워크플로우가 레포지토리 코드에 대해 실행하기 전 필수로 해야하는 체크아웃 작업(runner에 레포지토리를 체크아웃하는 액션)
      - uses: actions/setup-node@v3 # node.js (ver14)를 설치하는 actions/setup-node@v3 작업
        with:
          node-version: '14'
      - run: npm install -g bats    # run: runner가 명령을 하도록 작업 지시. 예) npm으로 테스팅 패키지 'bats'를 설치
      - run: bats -v # 버전을 출력하는 param과 함께 bat 명령 수행
```
3. 위 코드를 commit하고 레포지토리에 푸쉬(push)한다.
4. 이제 레포지토리에 변경된 코드를 푸쉬할 때마다 자동으로 작동할 것이다.

<br>

---
### 💭 GitHub Actions는 작업에 대해 보기 편한 페이지를 제공한다.

## 워크플로우 파일 시각화(Visualizing)
- 우리가 생성한 워크플로우 파일과 Github Actions 구성요소들이 어떻게 계층적으로 조직화되어있는지를 **다이어그램**으로 볼 수 있다.
- (위 코드) 1,2단계(`uses`)는 단일 작업을, 3,4단계(`run`)는 쉘 스크립트를 실행한다.

---

## workflow의 activity가 수행하는 것을 보기
- 워크플로우가 트리거됐을 때, 워크플로우를 수행하는 `workflow run`이 생성된다.
- 그후, 레포지토리의 `Actions` 탭에서 `run`의 절차를 시각 그래프로 볼 수 있고, <br>Github에서의 각 단계의 activity를 볼 수 있다.



> [참고글](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)