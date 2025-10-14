<script setup>
import { ref, onMounted } from 'vue'
import apiClient from '@/services/api'

const tasks = ref([])

onMounted(async () => {
  try {
    const response = await apiClient.get('/tasks')
    tasks.value = response.data
  } catch (error) {
    console.error('Erro ao buscar as tarefas:', error)
  }
})
</script>

<template>
  <main>
    <h1>Minha Lista de Tarefas</h1>
    <ul>
      <li v-for="task in tasks" :key="task.id">{{ task.title }} - ({{ task.status }})</li>
    </ul>
  </main>
</template>
