새로운 내용을 시작하기 전에, 브라우저의 console 창에서 document의 속성들을 확인해보자!

- document.URL
  - "file:///C:/vscode/vanilajs-momentum/index.html"
- document.head
  - ```<head>...</head>```

<h1>HTML in JavaScript</h1>

- 우선 index.html의 ```<body>```에 id가 있는 ```<h1></h1>```을 추가한다.
```
    <body>
        <h1 id="title">Grab me!</h1>
        <script src = "app.js" ></script>
    </body>
```

이 Grab me! 를 가져오려면 ? 
- console 창에서 ```document.getElementById("")```를 이용해보자.
- 해당 함수는 string을 인자로 받아야한다!
```
const title = document.getElementById("title");
console.dir(title);
```
console.log 대신 console.dir() 을 사용했다. element를 더 자세하게 보여주는 녀석이다!

- 출력된 title 태그를 펼쳐보면 엄청 많은 속성들이 나타난다.<br>
  autofocus, tagName, translate...등등
- <b>속성 변경</b>
1. autofocus : false로 되어있고, className: ""로 되어있는 속성을 바꿔보자.
```
<h1 autofocus class="hello" id="title">Grab me!</h1>
// 변경된 모습은 
autofocus : true 와 className: "hello"
```
- 현재 javascript는 HTML element를 가져오지만, HTML 자체를 보여주는게 아닌
object를 보여주는 것이다.

2. innerText
- HTML이 아닌, javascript에서 getElementById를 이용해 "title"을 가져와서 속성을 바꿔주었다.
```
const title = document.getElementById("title");

title.innerText = "Got you!"  //브라우저에 Got you! 가 나타난 것을 볼 수 있다.
console.dir(title)
```
- 속성 읽어오기- id와 className을 읽으려면
```
const title = document.getElementById("title");  // id를 이용해서 찾음

console.dir(title.id);   //title
console.log(title.className);  //hello
```

<b>사실 html 파일은 그다지 쓸모가 없다. 우리는 해당 html파일을 javascript에서 읽어올 수 있어야하므로 오늘 공부한 내용은 매~~우 중요하다!</b> 
- 앞으로도 우리는 HTML에서 항목들을 가져와서, JavaScript를 통해 항목들을 변경할 것이다.


<h1>Searching For Elements</h1>

1. id보다는 className
- 보통 id보다 className을 사용하거나, 혹은 둘다 사용한다. (id = "title" 보다 class="title")
- 즉, ```document.getElementById()``` 보다는 ```document.getElementByClassName()```을 사용

2. JavaScript 방식으로 찾기
- h1 태그를 가져와보자.
```
const title = document.getElementsByTagName("h1") //div, anchor, button 등등을 가져오는 function
console.log(title)  //현재는 h1태그가 1개뿐이니까 array에 1개 찍힘. 
```
- 만약 h1태그가 2개 있다면? ```HTMLCollection(2) [h1, h1]```로 찍힌다. 

3. CSS 방식으로 찾기
- querySelector() : HTML element를 CSS 방식(CSS Selector)으로 검색하는 function
  - html 파일에서 ```<div class = "hello"></div>```사이에 ```<h1>```태그를 넣은 후에 검색해보자.
```
const title = document.querySelector(".hello h1")   //hello 클래스 내부에 있는 h1태그를 가져와라(첫번째 것)
console.log(title)
// 여러개가 있을 경우, const title = document.querySelector(".hello h1:first-child"); 로 첫번째 element를 가져올 수 있음
```

- querySelectorAll() : 두개 이상의 element를 가져오는 function
```
const title = document.querySelectorAll(".hello h1");  //h1태그가 2개있으면 NodeList(2) [h1, h1]
console.log(title)
```

- ```<h1>Grab me 1!</h1>```처럼 태그까지 고대로 출력된다. 두개 이상부터는 똑같이 array로 출력된다. 

- ```document.getElementsByClassName()```을 할 때는 우리가 className을 가져온다는 걸 아니까 "hello"만 적었지만,<br> 
  ```querySelector()```에서는 className과 그 안의 h1을 모두 알려줘야한다.
- 자세히) ```.hello``` 클래스 이름이 hello인 / ```hi``` hi태그를 찾아라
- 우린 대부분 querySelector를 쓸 것임
  - 심지어 id를 얻을 때에도 ```const title = document.querySelector("#hello");``` <br>
    이는 getElementById('hello') 와 같음.<br>id는 #으로 나타냄.