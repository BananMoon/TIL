<h1> Today I Learned </h1>
1. Chrome app with VanilaJS (2)<br>

<h2> 1. Chrome app with VanilaJS (2)</h2>

> Nomad Coder의『바닐라JS로 크롬 앱 만들기』강의를 들으며 공부한 내용입니다.

## Start
- 이번에 클론코딩할 애플리케이션은 Chrome의 momentum!
- 구현해볼 기능은 다음과 같다.
  - 시계 생성
  - 사용자 이름 기억하기
  - To-Do 리스트 구현
  - 배경사진 무작위 변경
  - Geolocation 좌표 따내기
  - 해당 좌표의 지역과 날씨 정보 제공

## <All about JavaScript>
- 프론트엔드에 쓸수 있는 유일한 언어
- 자바스크립트는 모든 컴퓨터, 핸드폰에, 브라우저에 내장되어 있음
- HTML, CSS, JavaScript만으로 만들 수 있는게 매우 많음
- 3D로 이루어지는 것부터, socket.io를 이용해서 실시간 기능까지 모두 JavaScript로 가능
- 더 깊게 배우고 싶다면 프레임워크로 넘어가면 된다. 쉽게 말해, 도우미 라고 생각! like React Native
  - JavaScript 만으로 android와 iOS 앱을 만들 수 있게 함
- 그다음은 Electron : JS, HTML, CSS로 <b>데스크탑 앱</b>(웹 사이트를 다운로드받아서 쓰는 형태와 같음)을 만들 수 있음
  - Electorn으로 만들어진 앱 : VSCode, Facebook Messenger, Twitch, Slack, Figma
- 이제는 JavaScript로 프론트엔드를 만든 후에, 백엔드도 만들 수 있음

### <브라우저에서 JS 다루기>
브라우저화면 - 오른쪽 마우스 - 검사(Inspection) 클릭 - '콘솔' 창- alert("Hi") - 엔터 - 화면창에 알림으로 뜸.
  - 자바스크립트가 항상 상주해있으니까 가능!!!
  - 즉 자바스크립트는 웹브라우저에 어디든 존재함
- 이 콘솔 창에서 하는 것 대신 JavaScript 파일에서 실행해보는게 더 편리!

### <실습>
- vscode에서 app.js와 index.html 파일 생성 후, app.js에 우리가 브라우저에서 적었던 ```alert("Hi")```를 적어보자.
- 그리고 그걸 브라우저에 옮겨서 붙여보면?<br>
  우리가 원하는 알림창으로 뜨지 않음. 파일 안의 내용으로 뜸ㅋㅋ
- 원하는 것은 <b>브라우저가 이 JS파일을 실행</b>시키는 것 
- style.css 파일을 생성하고 다음과 같이 작성한 후, 웹 브라우저에 가져와도 똑같이 파일의 내용이 나타난다!
```
body{
    background-color: beige;
}
```
- 웹 브라우저와 css&js를 연결시키는 방법은 뭘까? 바로 <b>HTML</b>이다.<br>
  브라우저는 HTML을 열고, HTML은 CSS와 JS를 가져오는 것! 즉, HTMl이 접착제!
- 즉, 브라우저에서 index.html 파일을 여는 것.

### <HTML과 JavaScript를 연결하는 방법>
- 위에서 html 파일을 통해 브라우저와 연결을 한다고 했다. css파일과의 연동은 <head></head> 사이에서, javascript와의 연동은 <body></body> 사이에서 연결을 진행한다.
- 간단한 코드를 봐보자.
```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content = "IE=edge">
        <meta name = "viewport" content="width=device-width, initial-scale=1.0">
        <link rel = "stylesheet", href="style.css"
        # rel(lationship)은 stylesheet, href에는 css파일!d을 가져온다.
        <title>Momentum</title>
    </head>
    <body>
        <script src = "app.js" ></script>
        # javascript파일은 body에서 가져와서 실행한다.
    </body>
</html>
```
- 브라우저에서는 css엔진과 javascript 엔진이 돌아가는 것이다!
- 브라우저에서 오른쪽 마우스 클릭 후, '검색'을 봐보면
  1. Elements는 HTML
  2. Console은 JavaScript
  3. CSS는 Styles의 body<br>
  에서 확인할 수 있다.

- 이제 Console창은 항상 열어두는 걸 습관화하자! (에러 확인이 바로 가능하니까~)
