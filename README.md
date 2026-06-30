# JavaScript-with-CRUD
todo-app/
│
├── index.html
├── style.css
└── script.js
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>To-Do List</title>

<link rel="stylesheet" href="style.css">
</head>
<body>

<div class="container">

<h1>📝 To-Do List</h1>

<div class="input-area">

<input
id="taskInput"
type="text"
placeholder="Enter a new task..."
>

<button id="addBtn">
Add
</button>

</div>

<div class="filters">

<button class="filter active" data-filter="all">
All
</button>

<button class="filter" data-filter="active">
Active
</button>

<button class="filter" data-filter="completed">
Completed
</button>

</div>

<ul id="taskList"></ul>

<div class="footer">

<span id="count"></span>

<button id="clearCompleted">
Clear Completed
</button>

</div>

</div>

<script src="script.js"></script>

</body>
</html>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Arial,sans-serif;
}

body{

background:#f5f5f5;

display:flex;

justify-content:center;

padding:40px;

}

.container{

background:white;

padding:30px;

border-radius:10px;

width:500px;

box-shadow:0 10px 25px rgba(0,0,0,.15);

}

h1{

text-align:center;

margin-bottom:20px;

}

.input-area{

display:flex;

gap:10px;

margin-bottom:20px;

}

input{

flex:1;

padding:12px;

font-size:16px;

}

button{

padding:12px 18px;

cursor:pointer;

}

.filters{

display:flex;

justify-content:center;

gap:10px;

margin-bottom:20px;

}

.filter.active{

background:#2196f3;

color:white;

}

ul{

list-style:none;

}

li{

display:flex;

justify-content:space-between;

align-items:center;

padding:12px;

margin-bottom:10px;

border:1px solid #ddd;

border-radius:6px;

}

.task{

display:flex;

align-items:center;

gap:10px;

}

.completed span{

text-decoration:line-through;

opacity:.6;

}

.actions{

display:flex;

gap:8px;

}

.footer{

display:flex;

justify-content:space-between;

margin-top:20px;

}

@media(max-width:600px){

.container{

width:100%;

}

}
const input = document.getElementById("taskInput");
const addBtn = document.getElementById("addBtn");
const taskList = document.getElementById("taskList");
const filters = document.querySelectorAll(".filter");
const count = document.getElementById("count");
const clearCompleted = document.getElementById("clearCompleted");

let todos = JSON.parse(localStorage.getItem("todos")) || [];

let currentFilter = "all";

/* ---------- SAVE ---------- */

function save() {

localStorage.setItem(
"todos",
JSON.stringify(todos)
);

}

/* ---------- RENDER ---------- */

function render() {

taskList.innerHTML = "";

let filtered = todos.filter(todo => {

if(currentFilter==="active")
return !todo.completed;

if(currentFilter==="completed")
return todo.completed;

return true;

});

filtered.forEach(todo=>{

const li=document.createElement("li");

li.dataset.id=todo.id;

if(todo.completed){

li.classList.add("completed");

}

li.innerHTML=`

<div class="task">

<input
type="checkbox"
class="toggle"
${todo.completed ? "checked":""}
>

<span>${todo.text}</span>

</div>

<div class="actions">

<button class="edit">Edit</button>

<button class="delete">Delete</button>

</div>

`;

taskList.appendChild(li);

});

count.textContent=
`${todos.filter(t=>!t.completed).length} task(s) remaining`;

save();

}

/* ---------- CREATE ---------- */

function addTodo(){

const text=input.value.trim();

if(!text) return;

todos.push({

id:Date.now(),

text,

completed:false

});

input.value="";

render();

}

addBtn.addEventListener("click",addTodo);

input.addEventListener("keypress",e=>{

if(e.key==="Enter"){

addTodo();

}

});

/* ---------- EVENT DELEGATION ---------- */

taskList.addEventListener("click",e=>{

const li=e.target.closest("li");

if(!li) return;

const id=Number(li.dataset.id);

const todo=todos.find(t=>t.id===id);

/* DELETE */

if(e.target.classList.contains("delete")){

todos=todos.filter(t=>t.id!==id);

}

/* EDIT */

if(e.target.classList.contains("edit")){

const updated=prompt(
"Edit task",
todo.text
);

if(updated){

todo.text=updated.trim();

}

}

render();

});

/* ---------- TOGGLE ---------- */

taskList.addEventListener("change",e=>{

if(!e.target.classList.contains("toggle"))
return;

const li=e.target.closest("li");

const id=Number(li.dataset.id);

const todo=todos.find(t=>t.id===id);

todo.completed=e.target.checked;

render();

});

/* ---------- FILTER ---------- */

filters.forEach(btn=>{

btn.addEventListener("click",()=>{

filters.forEach(b=>b.classList.remove("active"));

btn.classList.add("active");

currentFilter=btn.dataset.filter;

render();

});

});

/* ---------- CLEAR COMPLETED ---------- */

clearCompleted.addEventListener("click",()=>{

todos=todos.filter(t=>!t.completed);

render();

});

/* ---------- INITIAL ---------- */

render();
