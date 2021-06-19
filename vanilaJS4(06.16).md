<h1>javascript가 브라우저를 어떻게 실행시키는지 알아보자!</h1>

- html은 (브라우저와 css&js 간) 접촉제라고 배웠다! <br>
  즉, html이 css와 javascript를 가져오는건데, javascript를 가져오는 이유는?
- 당연히 html과 상호작용하기위해! 즉, HTML의 element들을 js를 통해 읽고, 변경할 수 있다는 것이다!

<b>브라우저의 핵심 object</b>에 대해 봐보자.
- document!!!!! 브라우저에 이미 존재해 있는 객체!
  - document는 뭘까? 우리가 접근할 수 있는! html을 가리키는 <b>객체</b>!
  - console창에서 document를 쳐보면 index.html 파일에 우리가 작성한 코드가 (다음과 같이) 그대로 나타난 것을 확인할 수 있다!
```
<!DOCTYPE html>
<html>
    <head>
        <title></title> 등등..
    </head>
    <body></body>
    </html>
```

  - 그렇기 때문에 우리가 html파일 에서 정의한 title 등등을 브라우저에서 document라는 객체를 통해 보여주고 있는 것! (JavaScript 관점에서)
  - 쉽게 말해서, HTML 코드를 javascript 관점에서 보고 있는게 브라우저!

<b> 이게 왜 중요할까? </b>
- 내가 HTML document 객체(의 속성들)를 JavaScript로 가져와서 쓸 수 있기 때문이지
- 쓸 수 있다는 건?<br>
  이전에 object는 property를 바꿀 수 있었던 것 처럼 읽고, 변경할 수 있다는 것!
  
- 그럼 title 속성을 바꿔보자. 
- 브라우저의 console창에 ```document.title = "Hello! From JS!"``` 치면 신기하게도 브라우저의 이름이 바뀐다! 당연히 F5를 하면 다시 원래대로 돌아가지만, console 창 대신 app.js에 써볼까?
1. index.html에서는 ```<title>Hello! From HTML!</title>```
2. app.js에서 ```document.title = "Hello! From JS!"```

브라우저에는 어떤 title이 뜰까?
JavaScript에서 HTML 요소에 접근하면서, ```"Hello! From JS!"```가 뜬다.

- 이렇게 JavaScript가 HTMl에 접근할 때는 ```document.~~```로 시작한다.<br>
  (왜? 객체 document가 우리의 web site (HTML구조 기억나죠?)를 의미하기 때문!)

<h2>결론</h2>

1. 우리는 HTMl과 JavaScript를 연결하기 위해서 아무것도 하지 않아도된다는 것! 이미 연결되어있다는것 ㅎㅎ
2. HTMl에서 여러 정보를 가져올 수 있다는 것 어디서?
3. <b>JavaScript 코드에서</b> 그 정보들을 볼 수 있다는 것 + HTML의 항목도 추가할 수 있다! (변경 가능)


<h2>최종 코드</h2>
<b>app.js 파일</b>

```
document.title = "Hello! From JS!"
```
<b>index.html 파일</b>

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content = "IE=edge">
        <meta name = "viewport" content="width=device-width, initial-scale=1.0">
        <link rel = "stylesheet", href="style.css"/>
        <title>Hello! From HTML!</title>
    </head>
    <body>
        <script src = "app.js" ></script>
    </body>
</html>
```
