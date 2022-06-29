오늘은 두 목차를 달려보겠다!
<h2>1. Input Values</h2>
<h2>2. Form Submission</h2>

<h1>1. Input Values</h1>

> 이제 본격적으로 우리가 만들 Momentum App을 시작해볼 것이다. 우리가 오늘 해야할 일은<br>
> 1. 당신은 누구인가요?
> 2. 받은 정보를 저장하기

- getElementById()와 querySearch()를 사용할 때의 차이는
  전자는 Id를 찾는 걸 아니까 Id만 인자로 전달하면 되고, (`getElementByID('login-form')`)<br>
  후자는 classname, tagname 모두 검색이 가능하므로 id를 찾는 다는 것을 `'#'`으로 알려줘야함. (`document.querySelector('#login-form')`)

(1) HTML에 element로 Input text와 Button을 생성하고 이를 javascript로 끌어오자!
```
const loginForm = document.getElementById("login-form")
const loginInput = loginForm.querySelector("input");  //input과 button은 HTML element를 이용해서 찾을 수 있어.
const loginButton = loginForm.querySelector("button");
```
- 더 간단하게 구현하자면.. (querySelector를 쓸 때는 `#`을 잊지말자!)
```
const loginInput = document.querySelector("#login-form input");
const loginButton = document.querySelector("#login-form button");
``````

(2)이제 버튼 click 이벤트를 생성해보자. (animation 역할을 맡는 javascript에서)
```
function onLoginBtnClick() {}
loginButton.addEventListener("click", onLoginBtnClick);
```
- 버튼이 클릭됐을 때 console.log() 찍을 수도 있고~ input Text 내부를 보여줄 수도 있고!

```
function onLoginBtnClick() {
    console.dir(loingInput)
}
loginButton.addEventListener("click", onLoginBtnClick);
```
- 자세히 보여줄 때(input의 속성을 보여줄 때)는 `console.dir()` 사용할 수 있는 걸 잊지말자.
> input에 'ss'를 치고 console에 뜬 `input`을 펼쳐 확인해보니 `value` 속성에 해당 값이 들어있는걸 확인했다.<br>
> `console.dir(loginInput.value)`를 해보니 정말 text에 적은 값이 출력됐다!! 이렇게 입력된 정보를 저장할 수 있는 거군ㅎㅎ

> 문제가 하나 있다! input에 text를 입력하지 않았을 때는 Button을 클릭하지 못하게 해야한다.
> if-else문으로 해결해볼 수 있는데..!!! 두둥탁 다음 목차에 이어서 확인해봅시댜:)

<b>지금까지의 코드</b><br>
index.html 코드 (바뀐 body 태그 부분만!)
```
    <body>
      <div id='login-form'>
        <input type="text" placeholder="What is your name?"/>
        <button>Log In</button>
        <script src = "app.js" ></script>
      </div>
    </body>
```
app.js 코드
```
const loginForm = document.getElementById("login-form")
const loginInput = loginForm.querySelector("input");  //input과 button은 HTML element를 이용해서 찾을 수 있어.
const loginButton = loginForm.querySelector("button");

function onLoginBtnClick(){
    console.dir(loginInput.value)
}
loginButton.addEventListener("click", onLoginBtnClick)
```

<h1>2. Form Submission</h1>

- 나중에는 html에서 조작해줄 것이지만 우선 javascript에서 제어해보겠다.
```
function onLoginBtnClick(){
    string = loginInput.value
    if (string === '') {
        alert("Please write your name.");
    } else if (string.length > 15){
        alert("Your name is too long.");
    }
}
```
- 이렇게 js에서 if문으로 제어해줄 수 있지만 더 깔끔하고 쉽게 제어할 수 있도록 브라우저(HTML)에서 제공하는 기능을 이용해보자.

> input element 자체에 여러 기능이 있음. 위에서 한 <b>input이 필수로 있어야하는 것과 최대 길이가 15이어야하는 </b>
```
<input
    required
    maxlength="15"
    type="text"
    placeholder="What is your name?"/>
```
- 문제는 HTML에서 input의 유효성 검사(즉, input에서 입력한 조건을 체크)하지 않고 있어. 
- 유효성 검사를 하기 위해서는 `<input>`이 `<form>` 안에 있어야함!!! `div`였던 것을 `form`으로 바꿔주자.

- 브라우저를 새로고침하면 브라우저에서 제약을 주고 있는 것을 확인할 수 있다!
- 근데! button을 누를 때마다 페이지가 새로고침되면서 submit되는 걸 볼 수 있다.
> form 태그는 button을 누르거나 submit인 input(`<input type="submit"/>`)을 enter하면 form이 submit되는 규칙이 있기 때문! <br>
> 즉, <b>우리가 form 안에서 enter를 누르고 만약 input이 더 존재하지 않는다면 자동으로 submit된다.</b>

그렇다면, 이제 JS에서 button click 이벤트를 받을 필요가 없는 것이다.
- enter 혹은 버튼을 클릭해보면 알겠지만, text를 입력한 후에 enter를 치거나 button을 누르면 자동으로 submit된다. WoW

<b>이제 위에서 언급한 enter할 때마다 form이 submit되는 것을 막아줘야한다. See you Next Page:)</b>
