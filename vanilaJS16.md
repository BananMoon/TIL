## Saving To-Dos
- 이제 새로고침할 때마다 우리가 작성해논 todo list가 지워지지 않도록 '저장'해보자.
- 이전에 greetings.js에서 썼던 `localStorage`를 사용하면 된다!
- array를 생성해줘서 list가 생길 때마다 array에 추가해준 후, localStorage에 저장해줬다. (따로 함수 생성함)
```
const array = [];

function saveToDos() {
    localStorage.setItem("todos", array);

}

function handleToDoSubmit(event) {
    //추가
    array.push(newTodo);
    saveToDos();
}
```

### 이제 화면에도 나타나도록 해보자.
- 두가지 문제 
  1. 새로고침 시, 화면에 나타나지 않음
  2. 새로고침 시, localStorage에 저장된 array가 초기화됨

그전에, 화면에 db에 있는 것을 출력해주려면 string으로 되어있어야한다.<br>
현재 array는 `yy,dd` 처럼 string으로 되어 있지 않다. 이를 string으로 바꿔주는 기능은 `JSON.stringify()`. 무엇이든 string으로 바꿔준다!<br>
string을 db에 저장하려면,
`localStorage.setItem("todos", JSON.stringify(toDos));` -> ["sleep","study"] 로 출력된다. (예)<br>
-> 이제 우리는 화면에 array를 출력해줄 수 있다!

---
## Loading To-Dos (part1)
- "[1,2,3,4]"를 javascript가 이해할 수 있는 array로 변환하려면? `JSON.parse("[1,2,3,4]")`<br>
-> [1,2,3,4]로 출력된다.

- 이게 왜 필요할까?<br>
현재 localStorage에 저장돼있는 값을 꺼내보면 (`localStorage.getItem('todos');`) <br>`"[\"yy\",\"hh\",\"ff\"]"` 이렇게 **단순한 string으로 출력**된다.
- 이것을 javascript가 알아들을 수 있도록 변환해야한다.
`JSON.parse(localStorage.getItem('todos'));` -> `["yy","hh","ff"]` 처럼 **살아있는 array**가 된다.
- 이것을 왜 해야하나면, javascript가 각 array에 접근하기 위함이다.

### 1. 새로고침 시, 화면에 나타나지 않는 문제 해결
> `forEach`란? <br>
> 함수를 실행시키는데, array에 있는 item 각각에 대해 실행시키는 것!
- **item**?  submit event Listener가 기본으로 (인자로) 제공해주는 정보처럼 js도 지금 (forEach로) 처리돼고있는 item을 제공해줌.
```
function sayHello(item){
    console.log("this is the turn of ", item);
}
parsedToDos.forEach(sayHello); // item마다 접근해서 출력,처리.. 가능!
```
이외에도, 함수를 호출하는 것 대신 **arrow function**을 사용할 수 있다.

> arrow function (화살표 함수)
> - 짧은 함수를 작성할 때 사용
> ```
> parsedToDos.forEach((item) => console.log("this is the turn of ", item))
> ```


이제 console.log 대신 화면에 띄우는 기능을 해보자. 다행히도 이미 선언한 paintToDo 함수를 이용할 수 있다! (paintToDo 함수: list와 삭제버튼을 화면에 띄우는 기능을 하는 함수)

---
## Loading To-Dos (part2)
### 2. 새로고침 후 to-do 리스트를 submit했을 때, localStorage에 저장된 array가 초기화되는 문제 해결
항상 시작할 때, `const array = [];`로 인해 배열이 초기화되는 게 문제! 
- 새로고침을 하고 list에 입력해서 초기화되기 전, reset되지 않은 localStorage를 가져와서 array를 복원해야한다.
```
let toDos = [];
const TODOS_KEY = "todos";

function saveToDos() {   //db에 저장
    localStorage.setItem(TODOS_KEY, JSON.stringify(toDos));
    console.log(localStorage.getItem(TODOS_KEY));
}
const savedToDos = localStorage.getItem(TODOS_KEY);  // string

if (savedToDos) {
    const parsedToDos = JSON.parse(savedToDos);  // array
    toDos = parsedToDos;  // 빈 array에 parsedToDos 복원
    //array는 forEach function을 갖고 있음
    parsedToDos.forEach(paintToDo); // item마다 접근해서 출력,처리.. 가능!
}
```

### <전체코드>
```
const toDoForm = document.querySelector('#todo-form');
const toDoInput = document.querySelector('#todo-form input');
const toDoList = document.querySelector('#todo-list'); 

let toDos = [];
const TODOS_KEY = "todos";

function saveToDos() {
    localStorage.setItem(TODOS_KEY, JSON.stringify(toDos));
    console.log(localStorage.getItem(TODOS_KEY));
}
function handleTodoDelete(event) {
    console.log(event.target.parentNode);  //button이 event.target이고, 그 부모가 li.
    // 해당 li를 삭제하면 되지
    const delList = event.target.parentNode;
    delList.remove();
}
function paintToDo(newTodo){
    // 리스트 생성
    const li = document.createElement('li');
    const span = document.createElement('span');
    span.innerText = newTodo;
    li.appendChild(span);
    // 삭제 버튼 생성
    const delButton = document.createElement("button");
    delButton.innerText = "CLEAR 👌";
    li.appendChild(delButton);
    toDoList.appendChild(li);
    delButton.addEventListener("click", handleTodoDelete);
}
// 입력값 받기
function handleToDoSubmit(event) {
    event.preventDefault();
    const newTodo = toDoInput.value;
    toDoInput.value=""; //입력 완료 후 입력값 사라지게 하기
    // todo 저장하기
    toDos.push(newTodo);  //배열 추가
    //list와 delButton 생성하기
    paintToDo(newTodo);
    saveToDos();
}

toDoForm.addEventListener("submit", handleToDoSubmit);

//list가 화면에 보이도록
const savedToDos = localStorage.getItem(TODOS_KEY);  // string

if (savedToDos) {
    const parsedToDos = JSON.parse(savedToDos);  // array
    toDos = parsedToDos;
    //array는 forEach라는 function을 갖고 있음
    parsedToDos.forEach(paintToDo); // item마다 접근해서 todo 출력!
}
```