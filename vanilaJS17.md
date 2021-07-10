## Deleting To-Dos (part1)
> 문제! list를 delete해도 localStorage에서 지워지지 않는다.

- 어떤 item을 localStorage에서 delete해야할까?
- 현재 data의 형태에 랜덤 id가 생성되도록 하고 싶다.
- `Date.now() // 밀리초(1000분의 1초)를 주는 함수` 를 이용해서 랜덤 숫자를 줘보자.

- 원래는 toDos 배열에 text를 넣었지만, object(text와 id)를 넣어보겠다.
```
const TodoObj = {
    text: newTodo,
    id: Date.now();
}
```
- 해당 데이터(object)를 paintToDO() 에도 넣어주고 싶다.

paintToDO()에 text가 아닌 object를 줬으니 나눠서 저장해보자.<br>
(1) span = newTodo`.text`; <br>
(2) li.id = newTodo`.id`;<br>
elements를 봐보면 다음과 같이 id가 생성되어 있다.
`<li id="1625039379020"><span>hello</span><button>CLEAR 👌</button></li>`

---

## Deleting To-Dos (part2)
> 작성한 ToDos를 완료하면, 삭제할 수 있어야한다. 삭제 기능을 만들어보자.
- 우린 새로운 함수 `filter()`를 이용해서 list를 지울 것이다.

> filter 함수란?<br>
> iterator 값들 중 원하는 값들만 뽑아서 리스트를 만들어주는 함수이다.
> 형식 : `filter(function, iterator)`
> - iterator의 값들을 하나씩 function에 넣어서 True로 반환하는 값들을 필터링하여 list로 만들어주는 함수이다!

- array에서 값을 지울 때, 실제로는 지우는게 아닌, 그 값을 제외한 새로운 array를 만드는 것!이다.
- filter를 이용해보면, 호출하는 함수에서 true를 반환하면 해당 item을 가지고 가는 것이고, false를 반환하면 그 item은 새 array에 포함되지 않는다.
```
function sexyFilter() {
}
[1,2,3,4].filter(sexyFilter)  //리스트의 item마다 접근해서 sexyFilter()를 실행!
```
- 위와 같은 상황일 때, <br>
sexyFilter(1)<br>
sexyFilter(2)<br>
sexyFilter(3) => false<br>
sexyFilter(4)<br>
이처럼 3에서 False가 나온다면? javascript는 3을 뺀 1,2,4만 유지하게 된다.

> 결론! array의 item을 유지하고 싶다면, true를 리턴해야 한다.
- 만약, `const arr = [1234,5678,223,122,45,6775]` 에서 1000 넘는 것을 지우고 싶다면 다음과 같이 함수를 만들어주고, filter()를 이용해서 호출하면 된다.<br>
```
function sexyFunction(num) { return num<=1000 }  //num이 1000을 넘으면 False를 반환한다.
arr.filter(sexyFunction)
```
---
## Deleting To-Dos (part3)
> 이제, todos에 있는 list를 지워보자. 리스트에는 각기 id가 있으므로(위에서 생성해줌!), 그 id를 이용하면 된다.

- filter()는 변경된 새 array를 반환할 뿐, 기존 array를 변형하지 않는다.
- 새 array에 저장해주기! `const newarr = arr.filter(item => item > 2)`<br>
++ 함수 생성하지 않고 간단하게!
- 이제 localStorage에 있는 id와 비교해보자.
- 문자열-> 숫자 변환 메서드 : `parseInt()`
- 필터링된 배열을 생성한 후, db에 저장해주는 saveToDos() 를 호출해주면 끝이다!

<todo.js 전체 코드>
```const toDoForm = document.querySelector('#todo-form');
const toDoInput = document.querySelector('#todo-form input');
const toDoList = document.querySelector('#todo-list'); 

let toDos = [];
const TODOS_KEY = "todos";

function saveToDos() {   //db에 저장
    localStorage.setItem(TODOS_KEY, JSON.stringify(toDos));
    console.log(localStorage.getItem(TODOS_KEY));
}

function handleTodoDelete(event) { // X버튼을 눌렀을 때
    // 해당 li를 삭제
    const delList = event.target.parentNode;
    delList.remove();
    console.log(typeof(delList.id)); //해당 li의 id를 얻을 때는 string타입.
    //삭제
    toDos = toDos.filter((toDo) => toDo.id !== parseInt(delList.id)); //타입을 맞춰야함
    saveToDos();
}

function paintToDo(newTodo){
    // 리스트 생성
    const li = document.createElement('li');
    li.id = newTodo.id;  //추가
    const span = document.createElement('span');
    span.innerText = newTodo.text;   //object의 text로 입력해줘야함
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
    const TodoObj = {
        text: newTodo,
        id: Date.now(),
    };
    toDos.push(TodoObj);  //배열 추가
    //list와 delButton 생성하기
    paintToDo(TodoObj);
    saveToDos();
}

toDoForm.addEventListener("submit", handleToDoSubmit);

//list가 화면에 보이도록
const savedToDos = localStorage.getItem(TODOS_KEY);  // string

if (savedToDos) {
    const parsedToDos = JSON.parse(savedToDos);  // array
    toDos = parsedToDos;
    //array는 forEach라는 function을 갖고 있음
    parsedToDos.forEach(paintToDo); // array의 item마다 접근해서 출력,처리.. 가능!
}
```
