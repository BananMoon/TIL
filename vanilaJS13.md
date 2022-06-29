## Intervals

현재 폴더 상태
- VANILAJS-MOMENTUM
  - css
    - style.css
  - js  (기능들을 파일 별로 분리시킴)
    - clock.js
    - greetings.js


> interval이란?
- 매번 일어나야하는 무언가. 
- 매 2초마다 무슨 일이 일어나게 하고 싶은 경우 쓰는 기능
- ex) 매 2초마다 서버를 확인하거나, 주식시장 api를 가져와야하거나 등등 필요한 경우가 꽤 있다.
- javascript에 이미 그러한 기능을 해주는 tool이 존재! 
```setInterval(실행하고자하는 function, 호출 간격(ms))```
- 먼저 콘솔에 인사하는 것을 매 5초마다 실행시켜보자!
```
function sayHello(){
    console.log("hello");
}
setInterval(sayHello, 5000);
```
## Timeouts and Dates
setInterval() 외에도 동작은 다르지만 유사한 기능을 하는 setTimeout()이 있다.<br>
```setTimeout(sayHello, 5000)  // 동작은 전혀 다름```
- 정해진 시간 뒤 한 번만 실행되는데, 반복 실행을 하고 싶다면 반복 실행한 뒤, 종료 조건을 별도로 주는 방법이 있다.
- 사용 예제) 접속 후 일정 시간이 지나 로그아웃 처리하는 경우, 방문자가 사이트에 접속한 지 20-30초 지난 뒤 광고 팝업을 띄울 경우


> setTimeout과 setInterval
- 유사점 : 특정 코드나 함수를 의도적으로 지연한 뒤 실행하고 싶을 때 사용하는 이벤트 함수
- 차이점 : 
  - setTimeout: 처음 지정된 간격만큼 기다린 후, 지정된 코드를 실행한 뒤, 지정된 코드가 끝난 시점에 다시 setTimeout 함수를 호출한다. **따라서 먼저 실행중인 코드가 지연되더라도 모든 처리가 끝난 후, setTImeout을 호출하기 때문에 모두 실행된다.**
  - setInterval: 앞서 진행중인 코드의 완료유무와 상관없이 정해둔 시간 간격에 무조건 지정된 코드를 호출하고자 하지만, **지정된 시간에 이벤트가 중첩되거나 처리할 수 없는 상황인 경우, 이벤트를 큐에 저장하지만, 코드가 무시될 수 있다.**

## Date object
- javascript에서 제공하는 기능들이 있다.<br>
참고)https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date
- getDate(), getDay():0이 일요일, getFullYear(), getHours(), getMilliseconds(), getMinutes(), getMonth(), getSeconds(), getTime()

- 이것을 매초마다 호출해야한다!
- 그럼 Interval을 쓰면 되겠지?
```
//Date object를 먼저 만들어준다.
const date = new Date();
function getClock(){
    console.log(`${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`);
}
getClock();  //추가
setInterval(getClock, 1000);
```

- 실행시켜보면, 1초 지연이 있기 때문에 새로고침하자마자 시계가 출력되지 않는다. 
- 바로 출력되도록 하기 위해 setInterval() 호출 전에 getClock() 을 한번 호출해준다.

- 실행해보면 조금 아쉬운 점이 10시 4분 8초 일때, 10:4:8 로 나타난다는 것. 모두 두 글자로 만들어주려면?
`padStart(maxLength, max가 아니라면 추가할 string)`라는 function을 쓴다.
- 예를 들어서, `"1".padStart(2, "0")` 라면, "01"이 된다.<br>
  padEnd()는 뒤에 string을 채우는 것이다.
- padStart()는 string으로 받아야하므로 number를 string으로 바꿔준 후 padStart()를 이용한다.
```
function getClock() {
    const date = new Date();
    const hours = String(date.getHours()).padStart(2, "0");
    const minutes = String(date.getMinutes()).padStart(2, "0");
    const seconds = String(date.getSeconds()).padStart(2, "0");
    clock.innerText = `${hours}:${minutes}:${seconds}`;
}
getClock();
setInterval(getClock, 1000);
```

드디어 시계가 완성됐다!!!