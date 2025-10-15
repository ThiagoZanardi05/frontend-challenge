<script setup>
const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['close'])

const formatDate = (dateString) => {
  const options = {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit',
  }
  return new Date(dateString).toLocaleDateString('pt-BR', options)
}
</script>

<template>
  <div class="modal-overlay" @click.self="emit('close')">
    <div class="modal-content">
      <h3>Detalhes da Tarefa</h3>
      <h4>{{ task.title }}</h4>
      <p><strong>Descrição:</strong> {{ task.description || 'Nenhuma descrição fornecida.' }}</p>
      <p><strong>Status:</strong> {{ task.status }}</p>
      <p><strong>Criada em:</strong> {{ formatDate(task.created_at) }}</p>
      <p><strong>Última alteração:</strong> {{ formatDate(task.updated_at) }}</p>
      <button @click="emit('close')">Fechar</button>
    </div>
  </div>
</template>

<style scoped>
.modal-overlay {
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal-content {
  background-color: white;
  padding: 30px;
  border-radius: 8px;
  width: 500px;
  max-width: 90%;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}
.modal-content h3 {
  margin-top: 0;
  border-bottom: 1px solid #eee;
  padding-bottom: 10px;
}
.modal-content h4 {
  color: #3490dc;
}
.modal-content p {
  line-height: 1.6;
}
.modal-content button {
  display: block;
  margin-left: auto;
  margin-top: 20px;
  padding: 10px 20px;
  border: none;
  background-color: #666;
  color: white;
  border-radius: 4px;
}
.modal-content button:hover {
  background-color: #555;
}
</style>
