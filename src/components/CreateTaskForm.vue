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
</template>

<style scoped>
form {
  margin-top: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
}
div {
  margin-bottom: 10px;
}
label {
  display: block;
  margin-bottom: 5px;
}
input,
textarea {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
}
button {
  padding: 10px 15px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
button:hover {
  background-color: #369b70;
}
</style>
