<script setup>
import { ref, onMounted, watch } from 'vue'
import draggable from 'vuedraggable'

const todos = ref([])// List of todos
const name = ref('')// User's name
const inputContent = ref('')// Current todo's content input
// Watcher to automatically save the name
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })
// Function to add a new todo item
const addTodo = () => {
  if (inputContent.value.trim() === '' || todos.value === null) {
    return
  }
  // Push a new todo object to the todos array
  todos.value.push({
    content: inputContent.value, // Content of the todo
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
  // Reset input fields after adding a new todo
  inputContent.value = ''
}
// Function to remove a todo from the list
const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo) // Filter out the todo to delete it
}
// On mount, load stored dat
onMounted(() => {
  name.value = localStorage.getItem('name') || '' // Load name or set default
  todos.value = JSON.parse(localStorage.getItem('todos')) || [] // Load todos
})
</script>

<template>
   <!-- Main container -->
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE YOUR TODO-LIST</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          v-model="inputContent"
          placeholder="e.g. send email"
        />
        <input type="submit" value="Add task" />
      </form>
    </section>
    <!-- Todo list display -->
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <h4>Highest Priority</h4>
        <!-- Draggable list of task -->
      <draggable v-model="todos" :animation="200">
        <template #item="{element}">
          <div
            :key="element.createdAt"
           :class="{ 'todo-item': true, 'done': element.done }"
          >
          <label>
            <input type="checkbox" v-model="element.done" />
            <span :class="'bubble ' + element.category"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="element.content" :disabled="element.done" />
          </div>
          <div class="actions">
            <button @click="removeTodo(element)" class="delete-btn">Delete</button>
          </div>
          </div>
        </template>
      </draggable>
      <h4>Lowest Priority</h4>
    </section>
  </main>
</template>

<style>
/* Styling for todo items */
.todo-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.todo-item.done .todo-content input {
  text-decoration: line-through;
  color: #888;
}

.actions {
  margin-left: auto;
}

.delete-btn {
  background-color: #ff4136;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 3px;
}
</style>
