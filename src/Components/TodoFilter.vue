<script setup lang="ts">
defineProps<{
    filters: string[]
    activeFilter: string
}>()

const emit = defineEmits<{
    changeFilter: [filter: string]
}>()
</script>

<template>
    <div class="filters">
        <button v-for="f in filters" :key="f" :class="{ active: activeFilter === f }" @click="emit('changeFilter', f)">
            {{ f }}
        </button>
    </div>
</template>

<style scoped>
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