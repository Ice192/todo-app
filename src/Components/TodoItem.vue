<script setup lang="ts">
import { ref } from 'vue';

const isEditing = ref(false)
const editingBuffer = ref('')

const props = defineProps<{
    task: Task
}>()
function startEdit() {
    isEditing.value = true
    editingBuffer.value = props.task.text
}

function cancelEdit() {
    isEditing.value = false
    editingBuffer.value = ''
}

function finishEdit() {
    if (!isEditing.value) {
        return
    }

    const trimmed = editingBuffer.value.trim()

    emit('updateText', props.task.id, trimmed)

    isEditing.value = false
    editingBuffer.value = ''
}

type Task = {
    id: number
    text: string
    completed: boolean
    favorite: boolean
}


const emit = defineEmits<{
    remove: [id: number]
    toggleFav: [id: number]
    toggleCompleted: [id: number]
    updateText: [id: number, text: string]
}>()
</script>

<template>
    <li :class="{ done: task.completed }">
        <div class="task-content">
            <button class="delete" @click="emit('remove', task.id)">X</button>

            <button class="fav" @click="emit('toggleFav', task.id)">
                {{ task.favorite ? '★' : '☆' }}
            </button>

            <input class="task-checkbox" type="checkbox" :checked="task.completed"
                @change="emit('toggleCompleted', task.id)">

            <template v-if="isEditing">
                <input type="text" v-model="editingBuffer" class="edit-input" @keyup.enter="finishEdit"
                    @keydown.esc.prevent="cancelEdit" @blur="finishEdit">
            </template>

            <template v-else>
                <span class="task-text" @click="startEdit">
                    {{ task.text }}
                </span>
            </template>
        </div>
    </li>
</template>

<style scoped>
.task-content {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    gap: 0.5rem;
    width: 100%;
}

li {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    gap: 0.5rem;
    border-bottom: 1px solid #ccc;
}

.task-checkbox {
    flex: 0 0 auto;
}

.task-text {
    flex: 1;
    text-align: left;
}

li.done .task-text {
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

.fav {
    border: none;
    background: none;
}

.fav:hover {
    transform: scale(1.2, 1.2);
}

.edit-input {
    flex-grow: 1;
    padding: 0.5rem;
    border: 1px solid #333;
    border-radius: 6px;
    font-size: 1rem;
}
</style>