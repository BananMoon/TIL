<h2>복습</h2>
<b>Q. javascript를 통해 html의 내용을 가져올 수 있는 이유</b>

- index.html 파일에서 app.js를 import하기 때문.
```
<script src = "app.js" ></script>
```
- javascript를 통해 element를 가져오는 방법
```
const title = document.querySelector(".hello:first-child h1");  //hello 클래스 내부에 있는 첫번 째 h1태그를 가져와라
console.log(title) // <h1>Grab me 1!</h1>
console.dir(title) // h1이라는 object를 표시하는 element 속성들을 전체 볼 수 있음. 그중 on~ 항목들은 event관련!
```

<h4>element 관련 property들에 대하여</h4>

- h1 태그의 style 속성의 color속성을 건들여보자! h1태그의 글씨를 파란색으로 바꾸고 싶다면,
```
title.style.color = "blue";
```
ps: 우리가 javascript를 브라우저에서 자주 쓰일 용도는 event를 listening하는 것이다.


<h1>Events Part1.</h1>
이제 이벤트를 생성해보자!<br>
브라우저에서는 다양한 이벤트가 발생할 수 있는데 클릭 이벤트를 먼저 생성해보자.<br>

- 방법은 title에 addEventListener()로 이벤트를 추가하는데, 
- 인자로 ("이벤트명", 이벤트 발생시 실행될 함수명) 을 전달한다.
- 단지 javascript에게 handleTitleClick 함수를 전달해주는 것이므로 user가 event를 발생시켰을 때 js가 해당 함수를 실행한다!
```
function handleTitleClick() {   //클릭했을 때 실행될 함수
    title.style.background = "white"
    console.log("Title was clicked!")
}
title.addEventListener("click", handleTitleClick) //클릭 이벤트를 추가
```

<h1>Events Part2.</h1>

- 이벤트명들은 어떻게 찾을까? <b>구글링</b>!
- 구글창에 ```[찾고싶은 element명] html element mdn]```
- mdn이란 Mozilla Developer Network
- Web APIs 란 키워드가 포함된 페이지 open! (JavaScript관점)
- MDN 페이지에서 'HTML element'로 들어가면 여러 property들을 볼 수 있음<br>
  https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement
- 만약 구글링하기 귀찮다면, console.dir(element)했을 때 나오는 property 중 ```on```이 붙은 것들을 확인하면 됨.
- 예) onclick -> 이벤트명 : "click"

<h3>마우스가 element 위에 올려놓고 나올 때 이벤트를 발생시켜보자</h3>

```
function handleMouseEnter(){
    title.innerText = "Mouse is here!";
    title.style.color = "purple";
}
function handleMouseLeave() {
    title.innerText = "Mouse is gone!";
}
title.addEventListener("mouseenter", handleMouseEnter)
title.addEventListener("mouseleave", handleMouseLeave)
```

ps:javascript로도 style을 바꿀 수 있지만, css로 바꾸는 것이 맞아. 그래서 style을 CSS에서 바꾸는 방법도 알아볼 거임!!