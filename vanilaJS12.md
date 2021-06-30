## Saving Username
> 우리가 해야할 일은 User name을 기억하는 것.<br>
> 매우 자주 일어나는 일이니까 당연히 API가 있다. classList의 `add`, `remove`처럼! 바로 `"local storage"`이다.

### <Window.localStorage>
- https://developer.mozilla.org/ko/docs/Web/API/Window/localStorage
- document의 객체에 접근할 수 있다. 저장한 데이터는 브라우저 세션 간 공유된다.
- sessionStorage의 데이터는 페이지를 닫을 때 사라지지만, localStorage의 데이터는 페이지를 닫아도 만료되지 않는다!
- `setItem()`으로 local storage에 객체를 추가할 수 있다.
- 해당 momentum App - F12 - Application - Storage - Local storage - file:// 에서 확인할 수 있다.
```
localStorage.setItem("username", "Moon")  // 미니DB에 key-value로 저장
localStorage.getItem("username")          // Moon 출력
localStorage.removeItem("username")       // key-value 제거
```

**문제는 새로고침을 했을 때 username 입력 form이 계속 나타나는 것. username이 저장되어있다면 form이 나타나지않도록 해야한다.**

<h2>Loading Username</h2>

- `addEventListener`를 하기 전에, username이 저장되어있는지 local storage를 확인해줘야한다.
- local storage는 브라우저가 가지고있는 미니DB같은 것이다.
- if) local storage에 user정보가 있다? 
    - form을 hide하고, user에게 greeting한다.
- else) local storage에 user 정보가 없다?
    - form을 show한다.
```
# user 정보를 받아온다.
const savedUsername = localStorage.getItem("username");

# 값이 비었는지 확인한다. (기본값: form은 hidden)
if (savedUsername === null) { # username이 비어있다면 form을 보여준다.
    loginForm.classList.remove("hidden");
    loginForm.addEventListener("submit", onLoginSubmit);
} else {  # 인삿말을 저장하고, greeiting한다.
    greeting.innerText = `Hello ${username}`;
    greeting.classList.remove("hidden");
}
```

- 자주 등장하는 "username"을 변수로 만들어주고, 자주 쓰이는 명령어를 함수로 만들어주면 가독성이 좋아진다.
```
const loginForm = document.querySelector("#login-form");
const loginInput = document.querySelector("#login-form input"); 
const greeting = document.querySelector("#greeting");

const HIDDEN_CLASSNAME = "hidden"; //string 담는 변수는 대문자.
const USERNAME_KEY = "username";
function onLoginSubmit(event){
    event.preventDefault();             //이게 없으면 submit할 때마다 새로고침됨
    const username = loginInput.value;  //user이름 저장
    loginForm.classList.add(HIDDEN_CLASSNAME);
    localStorage.setItem(USERNAME_KEY, username);
    paintGreetings(username);
}
function paintGreetings(username) {
    greeting.innerText= `Hello ${username}`;
    greeting.classList.remove(HIDDEN_CLASSNAME);
}

const savedUsername = localStorage.getItem(USERNAME_KEY);  //없다면 null 반환

if (savedUsername ===null) {
    //show the form
    loginForm.classList.remove(HIDDEN_CLASSNAME);
    loginForm.addEventListener("submit", onLoginSubmit);

} else {
    //hide the form
    paintGreetings(savedUsername)
}
```