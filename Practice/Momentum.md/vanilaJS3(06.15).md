<h1>Data type</h1>

1. integer : 정수  ex) 1, 2, 100

2. float : 실수  ex) 1.5, 3.5, 100.5

3. string : 문자열 ex) "hi", "moonz"

4. console에게 메시지(숫자나 string)를 보내려면 ```console.log()```를 사용!

5. Variable 선언 type
- const는 변하지 않는 값
```
const a = 10;
const b = 2;
a=20; (X)
```

- 변수명은 camel case 관용을 따름
```
const veryLongVariableName = "moonz";  //JS의 variable 관용(camel case)
//참고) very_long_variable_name = 0; //Python의 variable 관용(snake_case)
```

- let은 변하는 값
```
let a = 5;
a = 6;  (O)
```

- 이전에는 ```var```을 사용했음. 문제는 업데이트되었을 때 알려주지 않는다는 것
- 이제는 const와 let을 사용해서 ```const```를 기본적으로 쓰다가, 업데이트가 필요한 변수는 ```let```을 사용 
- 단, let으로 선언한 변수는 그 다음 값을 수정할 때는 let 언급 x
<h3> 최종 : const를 기본적으로 사용하고, 변경하고싶으면 let</h3>
<br>

6. console에서 연산

```
console.log(a/b)
console.log(a+b)
console.log(a-b)
console.log('hello ' + veryLongVariableName)
```

7. boolean : true 와 false
- 사용 예시- 사용자가 로그인했는가? 비디오가 재생되고 있는가? 웹사이트가 재생되고 있는가?
```
const amIFat = true;
console.log(amIFat) //콘솔에 true
```

8. null: 변수에 아무것도 없음. 값은 있는데 nothing인 것 <--> false : false라는 값 
```
const amIThin = null
console.log(amIThin) //콘솔에 null
```

9. undefined : 컴퓨터 메모리안에는 존재하는데 값이 없는 것.
```
let something; 
console.log(something) //undefined 
```
- null과 undefined 차이
  - null은 변수안에 어떤것도 없다는 것을 표현하기위해 우리가 쓴 것. 의도적으로 사용
  - undefined는 값 자체가 주어지지 않은 것

10. Arrays : 하나의 variable 안에 데이터의 list를 가지도록 함
```
const daysOfWeek = ["mon", "tue", "thu", "fri", "sat"]
const nonsense = [1, 2, "hello", fale, null, true, undefined, '']
console.log(daysOfWeek)  //리스트 출력
console.log(daysOfWeek[0])  //mon 출력
daysOfWeek.push("sum") # 항목을 array에 추가
```

11. object
- 지금까지 배운걸로는 아래와 같이 선언해 줄 수 있다!<br>
  근데 player에 대한 속성으로 한번에 정리하고 싶다면?
- 리스트에 다양한 속성들을 넣고 싶다면?
```
const playerName = "moonz"
const playerPoints = 121212
const playerPretty = true
const plyerFat = "thin"
```

player라는 object를 선언하고 그 안에 property를 추가하는 방법!
```
const player = {
    name: "moonz",
    points: 121212,
    pretty: true,
    fat : "thin"
};
```
- 이렇게 되면 추가, 삭제, 수정이 매우 쉬워짐
```
console.log(player.name);  //moonz 출력
console.log(player["points"])  //121212 출력
player.fat = "little bit"  // 변경도 가능
console.log(player.fat)  // little bit 출력
```
- 지금 보니, console.log도 player.fat처럼 생겼네! console도 player처럼 하나의 object인걸 알 수 있군ㅎㅎ 
  - console.log(connsole)을 해보면 console이라는 object의 다양한 property들이 나타나는 것을 볼 수 있어!
  
<br>
<h4><b>Q.</b> const는 변경 불가인데 위에서 왜 에러가 안날까? 그건 object가 변경될 때 발생하는 에러이기 때문이지! object 안의 것을 변경하는건 문제없다 이소리야.</h4>

```
player = false; //다음과 같이 player를 변경할 경우? object 변경이므로 에러 발생!
```
- 추가도 할 수 있지롱
```
player.lastName = "potato"
```

12. function : 코드를 캡슐화해서, 실행을 여러번 할 수 있음
```
function sayHello(nameOfPerson) {  // nameOfPerson은 함수 내에서만 존재
    console.log(nameOfPerson)
}
sayHello("moonz")

function plus(a, b) {
    console.log(a, b)
}
plus(8, 60)  
```

<h4>만약 하나의 인자를 받는 함수에 여러 인자를 전달한다면?</h4>

```
function minusFive(num){
  console.log(num-5);
}
minusFive(5, 10, 12, 4, 34, 7) //0만 출력!
```
13. calculator : 나만의 계산기~
```
const calculator = {
    add: function(a,b){
        console.log(a+b);
    },
    minus: function(a,b){
        console.log(a-b);
    },
    devide: function(a,b){
        console.log(a/b);
    },
    square: function(a,b){
        console.log(a**b);
    }
};

calculator.add(5,2);    //7
calculator.minus(5,2);  //3
calculator.devide(5,2); //2.5
calculator.square(5,2); //25
```

14. Return
- 브라우저에서 일어나는 것(console.log())이나 알람(alert) 같은 것보다 우리가 필요한 것은 함수의 값을 코드상에서 얻는 것.
- 그때 return을 사용함

```
const age = 96;
function calculateKrAge(ageOfForeigner){
  return ageOfForeigner + 2;
}
const krAge = calculateKrAge(96);
console.log(krAge);
```

15. 조건문(conditionals)
- 사용 예시) 사용자가 무언가를 하면 반응을 해준다, 로그인하면 무언가를 보여준다.

(1) 사용자에게 입력 받는 prompt
- 메시지를 보여주며 사용자에게 값을 얻는 prompt
- <b>문제는!</b> javascript가 로딩 중. 즉 실행을 멈추게 하고 입력을 받는 prompt... 이제 사용하지 않음.
- css 적용도 안되고 아주 오래된 방법이야.. 
- 요즘에는 HTML과 CSS로 자신만의 창을 만듦!
```
const age = prompt("How old are you?");    
console.log(typeof age)  //typeof : 해당 변수의 타입. string임

parseInt(age) // parseInt() : string을 number로 변환
console.log(typeof age, typeof parseInt(age))  // string number
```

- 이제 <b>if문</b>을 사용해보자! 
```
const age = parseInt(prompt("How old are you?"));   
console.log(age)

if (isNaN(age) || age < 0) { 
    prompt("Please write a real positive age.");   
} else if (age < 18){
    console.log("You are too young.You can not drink.")
} else if (18 <= age && age <= 50){
    console.log("You can drink and drive.")
} else if (age === 100) {
    console.log("wow you are wise")
} else if ( 50 < age && age <= 80) {
    console.log("You should exercise")
}else if (80 < age) {
    console.log("How about stopping driving?")
} 
```
- isNaN() func : NaN인지 확인하는 function으로 boolean을 reuturn함
- and 연산 : &&
- or 연산 : ||
  - or연산자는 앞이 true면 뒤에 확인하지 않음.
- equal 연산 : ===
- non equal : !==
