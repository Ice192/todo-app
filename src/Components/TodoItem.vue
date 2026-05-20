<script setup lang="ts">
type Task = {
    id: number
    text: string
    completed: boolean
    favorite: boolean
}

defineProps<{
    task: Task
}>()

const emit = defineEmits<{
    remove: [id: number]
    toggleFav: [id: number]
    toggleCompleted: [id: number]
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

            <span class="task-text">
                {{ task.text }}
            </span>
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
</style>