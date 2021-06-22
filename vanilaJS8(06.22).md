# 8. vanilaJS8

## CSS in JavaScript part3

이전 글에서는 click 이벤트가 발생할 때 h1의 className이 사라지고~생기도록~ 했지만, 문제가 될 수 있는 방식이기 때문에! 

이번에는 **classList**를 사용해보자. class 들을 **목록**으로 작업할 수 있는 것!

- [다음 링크](https://developer.mozilla.org/ko/docs/Web/API/DOMTokenList)에 들어가보면 `DOMTokenList.contains()`가 보이는데, 이것은 **인자로 전달한 class가 HTML element의 class에 속해있는지 true, false 알려주는 function**이다.
- js 파일에서 classList를 이용하는 것으로 바꿔보면

```
function handleH1Click() {
    const clickedClass = "clicked";
    if(h1.classList.contains(clickedClass)) {
        h1.classList.remove(clickedClass);
    } else {
        h1.classList.add(clickedClass);
    }
}
```

- 이제 class Name을 아예 지우지 않고, 변경할 수 있다.
- classList에는 다음과 같은 설명이 있다.

> Allows for ***manipulation*** of element’s class content attribute as a set of whitespace-separated tokens through a DOMTokenList object.

- 그럼 무엇으로 ***조작***할수 있냐? classList의 `remove(), add()` function으로 list에서 class를 지우고 추가할 수 있다.
- 이것은 javascript를 이용하는 개발자들이 자주 해야하는 일이기 때문에 더 쉬운 function이 있다! 그게 바로 **toggle**! class name이 존재하는지 확인하는 역할이다.

> Removes a given token from the list and returns false. If token doesn’t exist it’s added and the function returns true.

- 즉 위의 코드를 아래 한 줄로 구현할 수 있는 아주 편리한 녀석ㅎㅎ `h1.classList.toggle("clicked");`

**이제 application을 만들어볼 단계까지 왔다!!! 다음 글에서 시작하겠다 :)**

### <오늘의 코드>

1. style.css 파일

```
body{
    background-color: beige;
}
h1 {
    color: cornflowerblue;
    transition:color .5s ease-in-out;
}
.clicked{
    color: tomato;
}
```

2. app.js 파일

```
const h1 = document.querySelector(".hello:first-child h1");  //hello 클래스 내부에 있는 h1태그를 가져와라

function handleH1Click() {   //클릭했을 때 실행될 함수
    //const clickedClass = 'clicked';
    h1.classList.toggle('clicked');
}
h1.onclick = handleH1Click; //클릭 이벤트를 추가
```

3. index.html 파일

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
        <div class="hello">
            <h1 class = "sexy-font">Click me!</h1>
        </div>
        <script src = "app.js" ></script>
    </body>
</html>
```