<script setup>
import { ref } from 'vue'
import apiClient from '@/services/api'

const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['task-deleted', 'task-updated'])
const isEditing = ref(false)
const editableTitle = ref(props.task.title)
const editableDescription = ref(props.task.description)

const deleteTask = () => {
  emit('task-deleted', props.task.id)
}

const enterEditMode = () => {
  isEditing.value = true
}

const cancelEdit = () => {
  isEditing.value = false
  editableTitle.value = props.task.title
  editableDescription.value = props.task.description
}

const saveTask = async () => {
  if (!editableTitle.value.trim()) {
    alert('O título é obrigatório.')
    return
  }
  try {
    const response = await apiClient.put(`/tasks/${props.task.id}`, {
      title: editableTitle.value,
      description: editableDescription.value,
    })

    emit('task-updated', response.data)
    isEditing.value = false
  } catch (error) {
    console.error('Erro ao atualizar a tarefa:', error)
    alert('Não foi possível salvar as alterações.')
  }
}
</script>

<template>
  <li>
    <div v-if="!isEditing" class="view-mode">
      <span>
        <strong>{{ task.title }}</strong> - ({{ task.status }})
        <p v-if="task.description">{{ task.description }}</p>
      </span>
      <div class="actions">
        <button @click="enterEditMode" class="edit-btn">Editar</button>
        <button @click="deleteTask" class="delete-btn">Apagar</button>
      </div>
    </div>

    <div v-else class="edit-mode">
      <form @submit.prevent="saveTask">
        <input type="text" v-model="editableTitle" required />
        <textarea v-model="editableDescription"></textarea>
        <div class="actions">
          <button type="submit" class="save-btn">Salvar</button>
          <button type="button" @click="cancelEdit" class="cancel-btn">Cancelar</button>
        </div>
      </form>
    </div>
  </li>
</template>

<style scoped>
li {
  padding: 10px;
  border-bottom: 1px solid #eee;
}
.view-mode,
.edit-mode form {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
}
p {
  font-size: 0.9em;
  color: #666;
  margin: 5px 0 0 0;
}
.actions button {
  margin-left: 10px;
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
.edit-btn {
  background-color: #3490dc;
  color: white;
}
.delete-btn {
  background-color: #e53e3e;
  color: white;
}
.save-btn {
  background-color: #42b983;
  color: white;
}
.cancel-btn {
  background-color: #666;
  color: white;
}

.edit-mode input,
.edit-mode textarea {
  width: 70%;
}
</style>
