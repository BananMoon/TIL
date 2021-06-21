<h2>More Events</h2>

단순한 것 이외에도 엄청 많은 것을 할 수 있음. <br>
우리가 이전에 했던 `title.addEventListener("mouseenter", handleMouseEnter);`을 이렇게도 적을 수 있음.<br>
->`title.onclick = handleTitleClick;`
<br>
다른 것도 다 바꿔보면
```
title.onclick = handleTitleClick; //클릭 이벤트를 추가
title.onmouseenter = handleMouseEnter;
title.onmouseleave = handleMouseLeave;
```
- 이전처럼 한 것의 장점은 나중에 `.removeEventListener`를 통해 해당 eventListener를 제거할 수 있음.
- 그래서 비록 길지만 이전처럼 event listener를 생성해줄 것임


<h3>Window Event Listener</h3>

- window element도 event가 있다.
- onmouseup, onmousewheel 등등..
- 여기서 우린 "onresize" 라는 이벤트를 listen해볼 것이다.
```
function handleWindowResize() {
    document.body.style.backgroundColor = "tomato";
}
window.addEventListener("resize", handleWindowResize);
```
- document의 body에 접근할 수 있는 게 너무 편리한 것 같다 ㅎㅎ 아쉬운 점은 이렇게 중요한 body, head, title 요소들만 가져올 수 있다는 점.
  - ex) body의 `<div>`는 가져오지 못한다.
  - 그런 요소들은 querySelector(All)로 가져와야함..

<h3>Clipboard events</h3>

- copy, cut, paste event가 있음!
```
function handleWindowCopy(){
    alert('copied!');
}
window.addEventListener("copy", handleWindowCopy);
```

<h3>Connection events></h3>

- offline, online 이벤트가 존재.
- wifi에 연결되어있는지를 알려줌


<h2>CSS in JavaScript Part1</h2>

- 조건문을 이용해서 mouse를 클릭할 때마다 color가 바뀌게 하려면?
```
const h1 = document.querySelector(".hello h1");

function handleH1Click() {
    if(h1.style.color === 'blue'){
        h1.style.color = 'tomato';
    } else {
        h1.style.color = 'blue';
    }
}
h1.addEventListener("click", handleH1Click);
```
color의 현재 상태를 저장해서 매번 color를 호출(h1.style.color)할 필요가 없게 하자.
```
function handleH1Click() {
    const currentColor = h1.style.color;  //첫 상태 저장할 const 변수
    let newColor;                         //변화할 상태를 저장할 let 변수
    if(currentColor === 'blue') {
        newColor = 'tomato';
    }else {
        newColor = 'blue';
    }
    h1.style.color = newColor;     //바뀐 상태를 저장하는 문장 (한번 호출)
}
```
- 훨씬 보기 좋아졌다!
- 지금까지 우리는
  1. element를 찾는다.
  2. event를 listen한다.
  3. 그 event에 react한다.


<h2>CSS in JavaScript Part2</h2>
여태까지는 Javascript에서 style을 변경해왔지만 별로 좋은 방법은 아니므로~ 이제 CSS로 style을 변경하는 걸 해볼거야!

- javascript는 animation에 적합한 도구이고, css는 style에 적합한 도구라고 알아두자!
- 참고로, html 파일에서 javascript와 css 파일을 모두 import하고 있는 것을 기억해두자. 
<br>

css에 몇개 추가를 해보면,
```
body{
    background-color: beige;
}
//추가
h1 {
    color: cornflowerblue;  //h1의 기본값 지정
}

.active{
    color: tomato;   //active라는 클래스를 어느 element에 지정해주면 해당 기본값이 적용됨!
}
```

이제 이 active 클래스를 html에 적용해보려면! javascript로 가보자.
javascript의 click 이벤트 함수에
`h1.className = 'active'`를 추가하면 click 이벤트가 발생할 때 해당 active클래스가 적용되서
potato 색으로 변한다!

- javascript가 HTML을 조정하면, css가 그 HTML을 바라보는 것으로 돌아가는 것이다. 
- 어떻게 적용할까?
```
function handleH1Click() { 
    if(h1.className === 'active') { // h1.className이 active라는 클래스이면 클래스이름을 없애고
        h1.className = '';
    } else{                         //active라는 클래스가 아니면
        h1.className = 'active';    //active 클래스를 적용한다. (tomato로 변환)
    }
}
```
- `.active{}` 클래스는 다른 이름으로도 바꿔줄 수 있다.
- `.clicked{}` 도 가능하고~ 바꿔주게되면 js에서도 바꿔줘야 함! `h1.className === 'clicked'`로..
