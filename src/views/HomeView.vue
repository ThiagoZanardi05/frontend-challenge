<script setup>
import { ref, onMounted } from 'vue'
import apiClient from '@/services/api'
import CreateTaskForm from '@/components/CreateTaskForm.vue'

const tasks = ref([])

const fetchTasks = async () => {
  try {
    const response = await apiClient.get('/tasks')
    tasks.value = response.data
  } catch (error) {
    console.error('Erro ao buscar as tarefas:', error)
  }
}

const handleTaskCreated = (newTask) => {
  tasks.value.push(newTask) // Adiciona a nova tarefa diretamente na lista
}

onMounted(fetchTasks)
</script>

<template>
  <main>
    <h1>Minha Lista de Tarefas</h1>

    <CreateTaskForm @task-created="handleTaskCreated" />

    <ul>
      <li v-for="task in tasks" :key="task.id">{{ task.title }} - ({{ task.status }})</li>
    </ul>
  </main>
</template>
