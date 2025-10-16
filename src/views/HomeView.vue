<script setup>
import { ref, onMounted, reactive, watch } from 'vue'
import apiClient from '@/services/api'
import CreateTaskForm from '@/components/CreateTaskForm.vue'
import TaskItem from '@/components/TaskItem.vue'
import TaskDetailModal from '@/components/TaskDetailModal.vue'

const params = reactive({
  page: 1,
  per_page: 5,
  sort_by: 'created_at',
  sort_dir: 'desc',
  status: '', // '' para todos, 'pendente' ou 'concluída'
})

const tasks = ref([])
const isLoading = ref(false)
const apiError = ref(null)
const pagination = ref(null)
const selectedTask = ref(null)

const fetchTasks = async () => {
  isLoading.value = true
  apiError.value = null
  try {
    const activeParams = { ...params }

    if (!activeParams.status) {
      delete activeParams.status
    }

    const response = await apiClient.get('/tasks', { params: activeParams })

    tasks.value = response.data.data
    pagination.value = response.data.meta
  } catch (error) {
    apiError.value = 'Não foi possível carregar as tarefas. Tente novamente mais tarde.'
    console.error('Erro ao buscar as tarefas:', error)
  } finally {
    isLoading.value = false
  }
}

watch(params, fetchTasks, { deep: true })

const handleTaskCreated = () => {
  fetchTasks()
}

const handleTaskUpdated = (updatedTask) => {
  const index = tasks.value.findIndex((task) => task.id === updatedTask.data.id)
  if (index !== -1) {
    tasks.value[index] = updatedTask.data
  }
}

const handleTaskDeleted = async (taskId) => {
  try {
    await apiClient.delete(`/tasks/${taskId}`)
    fetchTasks()
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

const goToPage = (page) => {
  if (page >= 1 && page <= pagination.value.last_page) {
    params.page = page
  }
}

onMounted(fetchTasks)
</script>

<template>
  <h1>Minha Lista de Tarefas</h1>
  <CreateTaskForm @task-created="handleTaskCreated" />

  <div class="filters">
    <select v-model="params.status">
      <option value="">Todos os Status</option>
      <option value="pendente">Pendente</option>
      <option value="concluída">Concluída</option>
    </select>
    <select v-model="params.sort_by">
      <option value="created_at">Data de Criação</option>
      <option value="title">Título</option>
      <option value="status">Status</option>
    </select>
    <select v-model="params.sort_dir">
      <option value="desc">Descendente</option>
      <option value="asc">Ascendente</option>
    </select>
  </div>

  <div v-if="isLoading" class="loading-state">A carregar tarefas...</div>
  <div v-else-if="tasks.length === 0" class="empty-state">Nenhuma tarefa encontrada.</div>
  <div v-if="apiError" class="error-state">{{ apiError }}</div>

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

  <div v-if="pagination" class="pagination-controls">
    <button @click="goToPage(pagination.current_page - 1)" :disabled="pagination.current_page <= 1">
      Anterior
    </button>
    <span>Página {{ pagination.current_page }} de {{ pagination.last_page }}</span>
    <button
      @click="goToPage(pagination.current_page + 1)"
      :disabled="pagination.current_page >= pagination.last_page"
    >
      Próxima
    </button>
  </div>

  <TaskDetailModal v-if="selectedTask" :task="selectedTask" @close="closeTaskDetails" />
</template>

<style scoped>
.filters {
  display: flex;
  gap: 15px;
  margin-bottom: 20px;
  background: #fdfdfd;
  padding: 15px;
  border: 1px solid #eaeaea;
  border-radius: 8px;
}

.filters select {
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.pagination-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  gap: 15px;
}

.pagination-controls button {
  padding: 8px 15px;
  background-color: #3490dc;
  color: white;
  border: none;
  border-radius: 4px;
}

.pagination-controls button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
