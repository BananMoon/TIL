## Getting Username
우리가 해야할 것은 user가 form을 입력하고 나면 
1. 해당 입력 칸이 사라진다.
2. 입력한 user에게 인삿말을 건넨다. -> `Hello {username}`
---
### 1. form을 제출하면 해당 form이 사라진다.
- 방법은 css를 이용해서 숨기는 것!
- 이전에 했던 것처럼 css에서 클래스 하나를 만들어서 그 안에 display 속성을 설정해주고, 그것을 브라우저에 적용하면 된다! js에서ㅎㅎ<br>

<style.css>
```
.hidden{
    display: none;
}
```
<app.js>
- submit event가 발생하면 hidden 클래스를 적용한다.
```
function onLoginSubmit(event){
    event.preventDefault(); # 기본동작 x
    const username = loginInput.value;
    event.classList.add("hidden");
}
```
---

### 2. 입력한 user에게 인삿말을 건넨다.
- 위에서 hidden 클래스를 추가해서 숨겼다면, 나타나게 하는 건 어떻게 할까?
- html파일에서 hidden클래스를 갖는 `<h1>`태그를 추가한다.
```
<h1 id="greeting" class="hidden"></h1>
```
- submit event가 발생했을 때 hidden클래스를 제거해주면 되겠죵?
```
const greeting = document.querySelector("#greeting");
const HIDDEN_CLASSNAME = "hidden";
function onLoginSubmit(event){
    event.preventDefault(); # 기본동작 x
    const username = loginInput.value;
    event.classList.add(HIDDEN_CLASSNAME);
    greeting.classList.remove(HIDDEN_CLASSNAME);
    # h1태그에 인삿말을 추가해줘야지! 
    greeting.innerText = `Hello ${username}`
}
```
- 변수명을 string에 추가할 때는 백틱을 추천한다.<br>
  백틱(``) 사이에는 `string ${valuename}`

**전체코드** - 가장 변화가 큰 app.js 파일만 보자.
```
const loginForm = document.querySelector("#login-form");
const loginInput = document.querySelector("#login-form input"); 
const greeting = document.querySelector("#greeting");

const HIDDEN_CLASSNAME = "hidden"; //string 담는 변수는 대문자.
function onLoginSubmit(event){
    event.preventDefault();             //이게 없으면 submit할 때마다 새로고침됨
    const username = loginInput.value;  //user이름 저장
    loginForm.classList.add(HIDDEN_CLASSNAME);
    console.log(username);
    greeting.innerText = "Hello " + username;
   //더 보기 좋은 방식(백틱``사용) :  greeting.innerText = `Hello ${username}`;
    greeting.classList.remove(HIDDEN_CLASSNAME);
}

//button이 아닌 submit을 감지해보자.
loginForm.addEventListener("submit", onLoginSubmit);
```

> 이렇게~ 유저한테 인사하기 성공!
이제 해야할 것은!
**우리가 유저를 기억해야한다.** See you Next Page :)