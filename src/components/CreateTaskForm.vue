<script setup>
import { ref } from 'vue'
import apiClient from '@/services/api'

const newTaskTitle = ref('')
const newTaskDescription = ref('')

const emit = defineEmits(['task-created'])

const handleSubmit = async () => {
  if (!newTaskTitle.value.trim()) {
    alert('O título é obrigatório.')
    return
  }

  try {
    const response = await apiClient.post('/tasks', {
      title: newTaskTitle.value,
      description: newTaskDescription.value,
    })

    emit('task-created', response.data)

    newTaskTitle.value = ''
    newTaskDescription.value = ''
  } catch (error) {
    console.error('Erro ao criar a tarefa:', error)
    alert('Não foi possível criar a tarefa.')
  }
}
</script>

<template>
  <div class="create-task-form">
    <form @submit.prevent="handleSubmit">
      <h3>Adicionar Nova Tarefa</h3>
      <div>
        <label for="title">Título:</label>
        <input type="text" id="title" v-model="newTaskTitle" required />
      </div>
      <div>
        <label for="description">Descrição:</label>
        <textarea id="description" v-model="newTaskDescription"></textarea>
      </div>
      <button type="submit">Adicionar Tarefa</button>
    </form>
  </div>
</template>

<style scoped>
.create-task-form {
  margin-top: 30px;
  margin-bottom: 30px;
  padding: 20px;
  background-color: #fdfdfd;
  border: 1px solid #eaeaea;
  border-radius: 8px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}

button {
  width: 100%;
  padding: 12px 15px;
  background-color: #3490dc;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}

button:hover {
  background-color: #2779bd;
}
</style>
