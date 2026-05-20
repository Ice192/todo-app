<script setup lang="ts">
import { computed, ref, watch, onMounted } from 'vue';

type Task = {
  id: number
  text: string
  completed: boolean
  favorite: boolean
}

const newTask = ref<string>('')
const tasks = ref<Task[]>([])

function addTask() {
  const text = newTask.value.trim()
  if (!text) {
    return
  }

  tasks.value.push({
    id: Date.now(),
    text: text,
    completed: false,
    favorite: false
  })

  newTask.value = ''

}

function removeTask(id: number) {
  tasks.value = tasks.value.filter((t) => t.id !== id)
}

const editingId = ref<number | null>(null)
const editingBuffer = ref<string>('')
function startEdit(task: Task) {
  editingId.value = task.id
  editingBuffer.value = task.text
}

function cancelEdit() {
  editingId.value = null
  editingBuffer.value = ''
}

function finishEdit(task: Task) {
  if (editingId.value !== task.id) {
    return
  }

  const trimmed = editingBuffer.value.trim()

  if (!trimmed) {
    removeTask(task.id)
  } else {
    task.text = trimmed
  }

  cancelEdit()
}

function toggleFav(task: Task) {
  task.favorite = !task.favorite
}

const search = ref('')
const activeFilter = ref('All')
const filters = ['All', 'Completed', 'Incompleted', 'Favorites']

const filteredTasks = computed(() => {
  return tasks.value.filter(t => t.text.toLowerCase().includes(search.value.toLowerCase())).filter(t => {
    if (activeFilter.value === 'Completed') return t.completed
    if (activeFilter.value === 'Incompleted') return !t.completed
    if (activeFilter.value === 'Favorites') return t.favorite
    return true
  })
})

watch(tasks, () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}, { deep: true })

onMounted(() => {
  const saved = localStorage.getItem('tasks')
  if (saved) {
    tasks.value = JSON.parse(saved)
  }
})
</script>

<template>
  <div class="wrapper">
    <h1>Todo App</h1>

    <div class="input-row">
      <input type="text" placeholder="add task here..." v-model="newTask">
      <button @click="addTask">Add</button>
    </div>

    <input type="text" placeholder="search here..." v-model="search" class="search-input">

    <div class="filters">
      <button @click="activeFilter = f" v-for="f in filters" :key="f" :class="{ active: activeFilter === f }">
        {{ f }}
      </button>
    </div>
    <ul class="task-list">
      <li v-for="task in filteredTasks" :key="task.id"
        :class="{ done: task.completed, editing: editingId === task.id }">

        <template v-if="editingId === task.id">
          <input type="text" v-model="editingBuffer" class="edit-input" @keyup.enter="finishEdit(task)"
            @keydown.esc="cancelEdit" @blur="finishEdit(task)">
        </template>

        <template v-else>
          <div class="task-content">
            <button class="delete" @click="removeTask(task.id)">X</button>
            <button class="fav" @click="toggleFav(task)">
              {{ task.favorite ? '★' : '☆' }}
            </button>
            <input class="task-checkbox" type="checkbox" v-model="task.completed">
            <span class="task-text" @click="startEdit(task)">{{ task.text }}</span>

          </div>
        </template>
      </li>
    </ul>
  </div>

</template>

<style scoped>
.wrapper {
  max-width: 500px;
  margin: 2rem auto;
  font-family: sans-serif;
  text-align: center;
}

.input-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

input {
  flex-grow: 1;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #ccc;
}

button {
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  cursor: pointer
}

.task-list {
  list-style: none;
  padding: 0;
  text-align: left;
}

.task-content {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 0.5rem;
  width: 100%;
}

.task-list li {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 0.5rem;
  /* padding: 0.5rem; */
  border-bottom: 1px solid #ccc
}

.task-checkbox {
  flex: 0 0 auto;
}

.task-text {
  flex: 1;
  text-align: left;
}

.task-list li span {
  text-align: left;
}

.task-list li.done span {
  text-decoration: line-through;
  opacity: 0.6;
}

.delete {
  background: #e53e3e;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 0.2rem 0.5rem;
  cursor: pointer;
}

.delete:hover {
  background: #e53030;
}

.editing .edit-input {
  flex-grow: 1;
  padding: 0.5rem;
  border: 1px solid #333;
  border-radius: 6px;
  font-size: 1rem;
}

.fav {
  border: none;
  background: none;
}

.fav:hover {
  transform: scale(1.2, 1.2);
}

.search-input {
  width: 100%;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1 solid #ccc;
  margin-bottom: 1rem;
}

.filters button {
  padding: 0.3rem 0.8rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  margin: auto 0.3rem;
}

.filters button.active {
  background: #333;
  color: #fff;
  border-color: #333;
}
</style>
