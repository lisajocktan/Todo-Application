<template>
  <div class="page font-mono">
    <div class="container pt-16">
      <h1 class="text-blue-500 text-2xl font-normal text-center pb-8">Todos</h1>

      <!-- Adding a task -->
      <form @submit.prevent="addTask">
        <div class="task-input flex mb-8 items-center">
          <input type="text" v-model="newTask" placeholder="Add a task" class="border-2 shadow-lg button shadow-blue-100">
          <button class="button shadow-lg shadow-blue-100 ml-2 px-6 py-2 bg-blue-500 text-white border-none rounded-md cursor-pointer" type="submit">Add</button>
        </div>
      </form>

      <hr class="divider" />

      <!-- Tabs for All Tasks and Completed Tasks -->
      <div class="tabs mb-5 space-x-6 text-center">
        <button @click="currentTab = 'all'" class="shadow-sm rounded-tl-lg rounded-tr-lg border-b-2 hover:bg-blue-500 hover:text-white border-blue-500 hover:shadow-lg px-4 py-2 bg-white button text-sm">All Tasks</button>
        <button @click="currentTab = 'completed'" class="shadow-sm rounded-tl-lg rounded-tr-lg border-b-2 hover:bg-green-500 hover:text-white border-green-500 hover:shadow-lg px-4 py-2 bg-white  text-sm">Completed Tasks</button>
      </div>

      <!-- Task list -->
      <div class="task-list" v-if="currentTab === 'all'">
        <div v-for="(task, index) in tasks" :key="index" class="task-row">
          <input type="checkbox" v-model="task.completed">
          <input type="text" :value="task.task" :class="{ 'completed-task': task.completed }" class="capitalize">
          <button @click="deleteTask(index)">Delete</button>
        </div>
      </div>

      <!-- Completed task list -->
      <div class="task-list" v-if="currentTab === 'completed'">
        <div v-for="(task, index) in completedTasks" :key="index" class="task-row">
          <input type="checkbox" v-model="task.completed">
          <input type="text" :value="task.task" :class="{ 'completed-task': task.completed }" class="capitalize">
          <button @click="deleteTask(index)">Delete</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const tasks = ref([]) // list of tasks
const newTask = ref('') // input for new task
const currentTab = ref('all')

// Sync the tasks from localStorage upon pageload
onMounted(() => {
  syncFromStorage()
})

// Adding a new task
const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({ task: newTask.value, completed: false })
    syncToStorage()
    newTask.value = ''
  }
}

// Deleting a task
const deleteTask = (index) => {
  tasks.value.splice(index, 1)
  syncToStorage()
}

// Syncs the tasks to localStorage
const syncToStorage = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}

// Syncs the localStorage to tasks
const syncFromStorage = () => {
  const storedTasks = localStorage.getItem('tasks')
  tasks.value = storedTasks ? JSON.parse(storedTasks) : []
}

// Computed property for completed tasks
const completedTasks = computed(() => {
  return tasks.value.filter(task => task.completed)
})
</script>

<style scoped>
.page {
  background: #f1f5f9;
  width: 100%;
  min-height: 100vh;
  padding-top: 2rem;
}

.container {
  max-width: 600px;
  margin: 0 auto;
}


.divider {
  margin: 2rem 0;
  border-color: #e2e8f0;
}

.task-input input[type="text"] {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.task-list .task-row {
  display: flex;
  align-items: center;
  margin-bottom: 0.5rem;
}

.task-list .task-row input[type="checkbox"] {
  margin-right: 0.5rem;
  margin-left: 10px;
}

.task-list .task-row input[type="text"] {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-left: 10px;
}

.task-list .task-row button {
  padding: 0.5rem 1rem;
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-left: 10px;
}

.completed-task {
  text-decoration: line-through;
}


</style>
