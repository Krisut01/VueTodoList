<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. make something"
          v-model="input_content"
        />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>School Acts</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :key="todo.id" :class="['todo-item', { done: todo.done }]">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="['bubble', todo.category]"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import { v4 as uuidv4 } from 'uuid';


const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

watch(name, (newVal) => localStorage.setItem('name', newVal))

watch(todos, (newVal) => localStorage.setItem('todos', JSON.stringify(newVal)), { deep: true })

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) return

  todos.value.push({
    id: uuidv4(),
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<style scoped>
/* Add these styles to enhance 3D effects */
.todo-item {
  transform: perspective(1000px) rotateX(0deg);
  transition: transform 0.3s ease;
}

.todo-item:hover {
  transform: perspective(1000px) rotateX(5deg) translateY(-5px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
}

.bubble {
  transition: transform 0.3s ease;
}

.todo-item:hover .bubble {
  transform: scale(1.1) translateZ(10px);
}

.create-todo input[type="submit"],
.todo-item .actions button {
  transition: all 0.3s ease;
}

.create-todo input[type="submit"]:hover,
.todo-item .actions button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
}

.create-todo input[type="submit"]:active,
.todo-item .actions button:active {
  transform: translateY(0);
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.greeting .title input {
  transition: all 0.3s ease;
}

.greeting .title input:focus {
  transform: translateY(-2px);
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
}
</style>
