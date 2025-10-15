<script setup>
import { ref } from 'vue'
import apiClient from '@/services/api'

const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['task-deleted', 'task-updated', 'show-details'])
const isEditing = ref(false)
const editableTitle = ref(props.task.title)
const editableDescription = ref(props.task.description)
const editableStatus = ref(props.task.status)

const showDetails = () => {
  emit('show-details', props.task)
}

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
  editableStatus.value = props.task.status
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
      status: editableStatus.value,
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
    <template v-if="!isEditing">
      <div
        class="task-info"
        @click="showDetails"
        style="cursor: pointer"
        title="Clique para ver os detalhes"
      >
        <strong>{{ task.title }}</strong> - <span>({{ task.status }})</span>
        <p v-if="task.description">{{ task.description }}</p>
      </div>
      <div class="actions">
        <button @click="enterEditMode" class="edit-btn">Editar</button>
        <button @click="deleteTask" class="delete-btn">Apagar</button>
      </div>
    </template>

    <template v-else>
      <div class="edit-form">
        <input type="text" v-model="editableTitle" required />
        <textarea v-model="editableDescription"></textarea>
        <select v-model="editableStatus">
          <option value="pendente">Pendente</option>
          <option value="concluída">Concluída</option>
        </select>
      </div>
      <div class="actions">
        <button @click="saveTask" class="save-btn">Salvar</button>
        <button type="button" @click="cancelEdit" class="cancel-btn">Cancelar</button>
      </div>
    </template>
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
