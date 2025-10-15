<script setup>
const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['task-deleted'])

const deleteTask = () => {
  emit('task-deleted', props.task.id)
}
</script>

<template>
  <li>
    <span> {{ task.title }} - ({{ task.status }}) </span>
    <div class="actions">
      <button @click="deleteTask" class="delete-btn">Apagar</button>
    </div>
  </li>
</template>

<style scoped>
li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #eee;
}
.actions button {
  margin-left: 10px;
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
.delete-btn {
  background-color: #e53e3e;
  color: white;
}
.delete-btn:hover {
  background-color: #c53030;
}
</style>
