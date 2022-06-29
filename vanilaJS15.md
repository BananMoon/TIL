## Adding ToDo
- background를 만든 과정과 비슷하게 TodoList를 만들어 보겠다.
- 추가할 것은 input, button이다. (button은 생략해도 됨)
HTML 파일
```
<form id = 'todo-form'>
    <input type="text" required placeholder = "Write a To Do and Press Enter"/>
    <button>저장</button>
</form>
<ul id='todo-list></ul>
```

- 그후, `js/todo.js` 파일을 생성한 후, 입력됐을 때의 이벤트(list 추가, default작동x)를 추가해줘야한다.
```
const toDoForm = document.querySelector('#todo-form');
const toDoInput = document.querySelector('#todo-form input');
const toDoList = document.querySelector('#todo-list'); 

// 입력값 받기
function handleToDoSubmit(event) {
    event.preventDefault();
    const newTodo = toDoInput.value;
    const li = document.createElement('li');
    const span = document.createElement('span');
    span.innerText = newTodo;
    li.appendChild(span);
    toDoList.appendChild(li);
    toDoInput.value=""; //입력 완료 후 입력값 사라지게 하기
}

toDoForm.addEventListener("submit", handleToDoSubmit);
```
---
## Deleting ToDos
- 먼저 버튼을 생성해보자.
- html : todo-list(id) 아래에 list와 삭제 버튼을 생성해줄 것이다. (js에서)
```
<ul id="todo-list>
</ul>
```
- 이제 list가 추가될 때 리스트(span) 옆에 button이 생성되도록 하자.
- 버튼을 생성하면서 span의 child로 추가하고, 버튼이 클릭됐을 때의 eventlistener도 추가한다.
```
function handleTodoDelete() {
}
//handleToDoList 함수 내
    const delButton = document.createElement("button");
    delButton.innerText = "CLEAR 👌";
    li.appendChild(delButton);
    delButton.addEventListener("click", handleTodoDelete);
```

- 버튼이 list마다 생기는데 모든 button이 같은 함수를 실행시킨다. 그럼 어떤 버튼이 눌린건지 알 수 있는 방법은?
- 이전에 언급했던 이벤트 발생 시 첫번째 주는 (default)인자에서 정보를 얻을 수 있다.
- `console.dir(event)`를 해보면 여러 property가 나오는데 그중 parentNode! 당연히 부모 노드는 `li`니까!
- `li.innerText`를 찍어보면 해당 button이 있는 li를 알 수 있다
- 이제 우리는 버튼이 클릭됐을 때 해당 li를 지워주면 된다.
```
function handleTodoDelete(event) {
    console.log(event.target.parentNode);  //button이 event.target이고, 그 부모가 li.
    // 해당 li를 삭제하면 되지
    const delList = event.target.parentNode;
    delList.remove();
}
```
참고) list를 화면에 띄우고, delbutton 생성 로직은 paintToDo() function으로 묶어서 호출해주도록 했다.
<br><br>

### 전체코드
```
const toDoForm = document.querySelector('#todo-form');
const toDoInput = document.querySelector('#todo-form input');
const toDoList = document.querySelector('#todo-list'); 

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
    //list와 delButton 생성하기
    paintToDo(newTodo);
    saveToDos();
}

toDoForm.addEventListener("submit", handleToDoSubmit);
```