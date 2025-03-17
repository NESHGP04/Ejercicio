<script>
    import { onMount } from "svelte";
  
    let task = "";
    let priority = "alta";
    let tasks = [];
  
    onMount(() => {
      loadTasks();
    });
  
    function addTask() {
      if (!task.trim()) {
        alert("Es necesario agregar una tarea!");
        return;
      }
  
      tasks = [...tasks, { task, priority, completed: false }];
      task = "";
      saveTasks();
    }
  
    function completeTask(index) {
      tasks[index].completed = !tasks[index].completed;
      tasks = [...tasks]; // Para activar reactividad
      saveTasks();
    }
  
    function deleteTask(index) {
      if (confirm("¿Estás seguro de borrar esta tarea?")) {
        tasks.splice(index, 1);
        tasks = [...tasks]; // Para actualizar la UI
        saveTasks();
      }
    }
  
    function clearAll() {
      if (confirm("¿Quieres borrar todas las tareas?")) {
        tasks = [];
        localStorage.removeItem("tasks");
      }
    }
  
    function saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(tasks));
    }
  
    function loadTasks() {
      tasks = JSON.parse(localStorage.getItem("tasks")) || [];
    }
  </script>
  
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 40px;
      background-color: #dbfaff;
    }
  
    .container {
      max-width: 400px;
      margin: auto;
      background-color: #49b5c8;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.45);
    }
  
    input, select, button {
      margin: 5px;
      padding: 10px;
      border-radius: 5px;
    }
  
    .task-list {
      list-style: none;
      padding: 0;
    }
  
    .task-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
      background-color: #eee;
      margin: 10px 0;
      border-radius: 5px;
      cursor: pointer;
    }
  
    .completed {
      text-decoration: line-through;
      color: gray;
    }
  
    .alta { color: red; }
    .media { color: orange; }
    .baja { color: green; }
  </style>
  
  <div class="container">
    <h1>ToDo</h1>
  
    <input bind:value={task} type="text" placeholder="Ingresa tu tarea..." />
  
    <select bind:value={priority}>
      <option value="alta">Alta</option>
      <option value="media">Media</option>
      <option value="baja">Baja</option>
    </select>
  
    <button on:click={addTask}>Agregar</button>
    <button class="deleteBtn" on:click={clearAll}>Limpiar</button>
  
    <ul class="task-list">
      {#each tasks as t, i}
        <li class="task-item {t.completed ? 'completed' : ''}" on:click={() => completeTask(i)}>
          <span>{t.task}</span>
          <span class={t.priority}>[{t.priority.toUpperCase()}]</span>
          <button class="deleteBtn" on:click={e => { e.stopPropagation(); deleteTask(i); }}>x</button>
        </li>
      {/each}
    </ul>
  </div>  