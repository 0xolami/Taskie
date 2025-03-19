<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Taskie</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
     /* Background Styling */
 body {
     margin: 0;
     font-family: 'Arial', sans-serif;
     background: url('https://via.placeholder.com/1200x800?text=Beautiful+Background') no-repeat center center fixed;
     background-size: cover;
     color: #333;
 }

 /* Container and Basic Styles */
 .container {
     background: rgba(75, 72, 72, 0.8);
     max-width: 600px;
     margin: 50px auto;
     border-radius: 8px;
     padding: 20px;
     box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
     text-align: center;
 }

 h1 {
     margin-bottom: 20px;
     font-size: 2em;
     color: #0f8dbe;
 }

 .form-group {
     margin-bottom: 10px;
 }

 input[type="text"],
 select,
 input[type="date"],
 input[type="time"] {
     width: calc(100% - 24px);
     padding: 12px;
     border: 1px solid #ccc;
     border-radius: 4px;
     margin-bottom: 10px;
 }

 select {
     cursor: pointer;
 }

 .todo-list {
     margin-top: 5px; 
     display: grid;
     grid-template-columns: 1fr 1fr;
     gap: 10px;
     list-style-type: none;
     padding: 0;
 }

 .todo-item {
     display: flex;
     flex-direction: column;
     justify-content: space-between;
     padding: 10px;
     background: #fff;
     border-radius: 8px;
     box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
 }

 .todo-item.completed span {
     text-decoration: line-through;
     color: #999;
 }

 .task-header {
     display: flex;
     justify-content: space-between;
     align-items: center;
 }

 .task-header span {
     flex-grow: 1;
     margin-left: 10px;
     text-align: left;
 }

 .buttons {
     display: flex;
     justify-content: space-between;
     margin-top: 10px;
 }

 .buttons button {
     background-color: #007bff;
     color: #fff;
     border: none;
     border-radius: 4px;
     padding: 8px 12px;
     cursor: pointer;
     transition: background-color 0.2s;
 }

 .buttons button:hover {
     background-color: #0056b3;
 }

 .edit-button {
     background-color: #ffc107;
 }

 .edit-button:hover {
     background-color: #e0a800;
 }

 .priority {
     width: 90px;
     text-align: center;
     background-color: #007bff;
     border-radius: 5px;
     color: #fff;
     padding: 8px 12px;
 }
  </style>
</head>
<body>
  <div class="container">
    <h1>Taskie</h1>
    <div class="form-group">
      <input type="text" id="task-input" placeholder="Enter a new task...">
    </div>
    <div class="form-group">
      <select id="priority-select">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
    </div>
    <div class="form-group">
      <input type="date" id="date-input">
      <input type="time" id="time-input">
    </div>
    <div class="buttons">
      <button onclick="addTask()">Add Task</button>
    </div> <br> <br> <br> <br>
    <ul class="todo-list" id="todo-list">
      <!-- Todo items will be dynamically added here -->
    </ul>
  </div>

  <script>
    // Function to add a new task to the list
function addTask() {
  const taskInput = document.getElementById("task-input");
  const taskText = taskInput.value.trim();
  const prioritySelect = document.getElementById("priority-select");
  const priority = prioritySelect.value;
  const dateInput = document.getElementById("date-input");
  const taskDate = dateInput.value;
  const timeInput = document.getElementById("time-input");
  const taskTime = timeInput.value;

  if (taskText !== "" && taskDate !== "" && taskTime !== "") {
    const todoList = document.getElementById("todo-list");
    const todoItem = document.createElement("li");
    todoItem.className = "todo-item";
    todoItem.innerHTML = `
        <div class="task-header">
          <input type="checkbox" onclick="toggleComplete(this)">
          <span>${taskText}</span>
          <span>${taskDate} ${taskTime}</span>
        </div>
        <div class="buttons">
          <span class="priority">${priority}</span>
          <button class="edit-button" onclick="editTask(this)">Edit</button>
          <button onclick="deleteTask(this)">Delete</button>
        </div>
      `;
    todoList.appendChild(todoItem);
    taskInput.value = "";
    dateInput.value = "";
    timeInput.value = "";

    saveTasks();
    scheduleNotification(taskText, taskDate, taskTime);
  }
}

// Function to delete a task from the list
function deleteTask(button) {
  const todoItem = button.parentNode.parentNode;
  todoItem.remove();
  saveTasks();
}

// Function to toggle task completion
function toggleComplete(checkbox) {
  const todoItem = checkbox.parentNode.parentNode.parentNode;
  todoItem.classList.toggle("completed");
  saveTasks();
}

// Function to edit a task
function editTask(button) {
  const todoItem = button.parentNode.parentNode;
  const taskSpan = todoItem.querySelector(".task-header span:nth-child(2)");
  const newTaskText = prompt("Edit task:", taskSpan.textContent);

  if (newTaskText !== null) {
    taskSpan.textContent = newTaskText.trim();
    saveTasks();
  }
}

// Function to save tasks to local storage
function saveTasks() {
  const tasks = [];
  const todoItems = document.querySelectorAll(".todo-item");
  todoItems.forEach((item) => {
    const taskText = item.querySelector(
      ".task-header span:nth-child(2)"
    ).textContent;
    const taskDate = item.querySelector(
      ".task-header span:nth-child(3)"
    ).textContent;
    const priority = item.querySelector(".priority").textContent;
    const completed = item.classList.contains("completed");
    tasks.push({ taskText, taskDate, priority, completed });
  });
  localStorage.setItem("todoTasks", JSON.stringify(tasks));
}

// Function to load tasks from local storage
function loadTasks() {
  const tasks = JSON.parse(localStorage.getItem("todoTasks")) || [];
  tasks.forEach((task) => {
    const todoList = document.getElementById("todo-list");
    const todoItem = document.createElement("li");
    todoItem.className = "todo-item";
    if (task.completed) {
      todoItem.classList.add("completed");
    }
    todoItem.innerHTML = `
        <div class="task-header">
          <input type="checkbox" onclick="toggleComplete(this)" ${
            task.completed ? "checked" : ""
          }>
          <span>${task.taskText}</span>
          <span>${task.taskDate}</span>
        </div>
        <div class="buttons">
          <span class="priority">${task.priority}</span>
          <button class="edit-button" onclick="editTask(this)">Edit</button>
          <button onclick="deleteTask(this)">Delete</button>
        </div>
      `;
    todoList.appendChild(todoItem);
  });
}

// Function to schedule a notification
function scheduleNotification(taskText, taskDate, taskTime) {
  const taskDateTime = new Date(`${taskDate}T${taskTime}`);
  const delay = taskDateTime.getTime() - Date.now();
  if (delay > 0) {
    setTimeout(() => {
      if (Notification.permission === "granted") {
        new Notification("Reminder", {
          body: `Task Reminder: ${taskText}`,
        });
      }
    }, delay);
  }
}

// Request notification permission on load
window.onload = () => {
  loadTasks();
  if (Notification.permission !== "granted") {
    Notification.requestPermission();
  }
};

  </script>
</body>
</html>
