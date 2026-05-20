<script setup lang="ts">
import { ref } from 'vue'

const newTask = ref('')

const emit = defineEmits<{
    add: [text: string]
}>()

function submitTask() {
    emit('add', newTask.value)
    newTask.value = ''
}
</script>

<template>
    <div class="input-row">
        <input type="text" placeholder="add task here..." v-model="newTask">

        <button @click="submitTask">Add</button>
    </div>
</template>

<style scoped>
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

.input-row {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1rem;
}
</style>