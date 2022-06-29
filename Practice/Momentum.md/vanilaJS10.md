<h2>1. Events</h2>

> 브라우저는 엔터를 치거나 button을 누를 때 새로고침(F5)을 하고 form을 submit하도록 되어있음.
이런 기본동작이 발생하지 않게 하려면?
- 우선 코드를 변경해보자. 이제 button이 아닌 submit 이벤트에 대해 적어봐야해.
```
const loginInput = document.querySelector("#login-form input");
function onLoginSubmit(info) {
    info.preventDefault();
    console.log(loginInput.value);
}
loginForm.addEventListener("submit", onLoingSubmit);
```
- function의 인자 `info`는 뭘까? 사실 JS는 우릴 위해 인자 한개를 자동으로 제공해주는데,<br>
  방금 발생한 event에 대한 정보를 첫번 째 인자로 주는 것!
- function에서 기본 정보를 받고자 한다면 인자를 채우면 된다!

- 새로 호출된 `info.preventDefault()` function은<br>
  어떤 event의 **기본 행동**이든지 발생되지 않도록 막는 것. 즉 브라우저가 기본적으로 form이 submit되면 페이지를 새로고침하는 것과 같은 기본적으로 발생하는 것을 하지 않도록 한다!</b>
- 이 function은 첫번째 인자(info) 안에 있는 function!
- 이제 App을 실행해보면 login 버튼을 눌러서 이벤트가 발생해도, *새로고침이 되지 않음*.


<h2>2. Events Part2</h2>
지난 파트에서 form의 기본 동작에 대해 알았다. 바로 <b>submit되는 것!</b> <br>
그럼 링크를 연결하는 anchor element의 기본 동작에 대해 알아보겠다.<br>

html파일에서 `<form>` 태그 아래에 `<a href="https://nomadcoders.co"></a>`를 추가한다.

- 그럼 link의 기본동작은? 다른 페이지로 이동하는 것
- js파일에서 link 이벤트를 생성해보자. 링크를 클릭했을 때 알람을 띄운 후에 다른 페이지로 이동하게 된다.
```
const link = document.querySelector("a");
function handleLinkClick(event) {
    alert('clicked!');
}
link.addEventListner("click", handleLinkClick);
```

> 위에서 말했듯이, 이벤트가 발생하면 JS가 함수를 실행시키면서 첫번째 인자로 object(information about the event that just happened)를 넣어준다.
> - 그럼 이 object를 function에서 받아줘볼까? 그냥 (`event`)를 넣어주면 된다. `console.log(event)`로 찍어보자.
> - (클릭으로 발생했으니까) *MouseEvent*라고 뜨면서 ScreenX:761, screenY: 200 등등 정보가 나왔다.
- 위의 코드에서는 알람이 뜨고 바로 다른 페이지로 넘어가는데 그 기본 동작을 막으려면? 위에서 했던 것처럼<br>
`event.preventDefault();`를 넣어주면 된다.
- 이제 `console.dir(event)`로 자세히 봐보면, 아래 외에도 다양한 것들이 있다.
1. defaultPrevented: true
2. path: (5) [a, body, html, document, Window]  
3. clientX: 18 clientY: 48

<h3>전체 코드</h3>

app.js
```
const loginForm = document.querySelector("#login-form");
const loginInput = document.querySelector("login-form input"); 
const link = document.querySelector("a");

function onLoginSubmit(event){
    info.preventDefault();
//    const username = loginInput.value;
    console.log(loginInput.value);
}

function handleLinkClick(event) {
    event.preventDefault();
    console.dir(event)
}
//button이 아닌 submit을 감지해보자.
loginForm.addEventListener("submit", onLoginSubmit);
link.addEventListener("click", handleLinkClick);
```
index.html
```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content = "IE=edge">
        <meta name = "viewport" content="width=device-width, initial-scale=1.0">
        <link rel = "stylesheet", href="style.css"/>
        <title>Momentum App</title>
    </head>
    <body>
      <form id='login-form'>
        <input
        required
        maxlength="15"
        type="text"
        placeholder="What is your name?"/>
        <button>Log In</button>
      </form>
      <a href="https://nomadcoders.co">Go to courses</a>
      <script src="app.js"></script>
    </body>
</html>
```