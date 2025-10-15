<script setup>
import { ref, onMounted } from 'vue'
import apiClient from '@/services/api'
import CreateTaskForm from '@/components/CreateTaskForm.vue'
import TaskItem from '@/components/TaskItem.vue'
import TaskDetailModal from '@/components/TaskDetailModal.vue'

const tasks = ref([])
const selectedTask = ref(null)

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

const handleTaskUpdated = (updatedTask) => {
  // Encontra o índice da tarefa antiga no array
  const index = tasks.value.findIndex((task) => task.id === updatedTask.id)
  if (index !== -1) {
    // Substitui a tarefa antiga pela nova
    tasks.value[index] = updatedTask
  }
}

const handleTaskDeleted = async (taskId) => {
  try {
    await apiClient.delete(`/tasks/${taskId}`)
    // Remove a tarefa da lista local sem precisar buscar tudo de novo
    tasks.value = tasks.value.filter((task) => task.id !== taskId)
  } catch (error) {
    console.error('Erro ao apagar a tarefa:', error)
    alert('Não foi possível apagar a tarefa.')
  }
}

const showTaskDetails = (task) => {
  selectedTask.value = task
}

const closeTaskDetails = () => {
  selectedTask.value = null
}

onMounted(fetchTasks)
</script>

<template>
  <h1>Minha Lista de Tarefas</h1>
  <CreateTaskForm @task-created="handleTaskCreated" />

  <ul>
    <TaskItem
      v-for="task in tasks"
      :key="task.id"
      :task="task"
      @task-deleted="handleTaskDeleted"
      @task-updated="handleTaskUpdated"
      @show-details="showTaskDetails"
    />
  </ul>
  <TaskDetailModal v-if="selectedTask" :task="selectedTask" @close="closeTaskDetails" />
</template>
