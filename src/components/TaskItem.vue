<script setup>
const props = defineProps({
  task: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['delete', 'toggle-complete'])

const handleDelete = () => {
  if (confirm('Удалить эту задачу?')) {
    emit('delete', props.task.id)
  }
}

const handleToggle = () => {
  emit('toggle-complete', props.task.id)
}
</script>

<template>
  <div class="task-item" :class="{ completed: task.completed }">
    <div class="task-header">
      <div class="task-header-left">
        <input 
          type="checkbox" 
          :checked="task.completed"
          @change="handleToggle"
          class="task-checkbox"
        />
        <span class="task-id">#{{ task.id }}</span>
        <h3 class="task-title">{{ task.title }}</h3>
      </div>
      <button @click="handleDelete" class="btn-delete" title="Удалить">
        🗑️
      </button>
    </div>
    <p class="task-text" v-if="task.text">{{ task.text }}</p>
    <div class="task-status">
      {{ task.completed ? '✅ Выполнено' : '⏳ В процессе' }}
    </div>
  </div>
</template>

<style scoped>
.task-item {
  background: white;
  border-radius: 15px;
  padding: 25px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  border: 2px solid transparent;
  position: relative;
}

.task-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.2);
  border-color: #667eea;
}

.task-item.completed {
  background: #f5f5f5;
  opacity: 0.8;
}

.task-item.completed .task-title,
.task-item.completed .task-text {
  text-decoration: line-through;
  color: #9e9e9e;
}

.task-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 15px;
  gap: 10px;
}

.task-header-left {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
  min-width: 0;
}

.task-checkbox {
  width: 20px;
  height: 20px;
  cursor: pointer;
  flex-shrink: 0;
}

.task-id {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.75em;
  font-weight: 600;
  flex-shrink: 0;
}

.task-title {
  margin: 0;
  font-size: 1.2em;
  color: #2c3e50;
  font-weight: 600;
  line-height: 1.3;
  word-break: break-word;
}

.task-text {
  margin: 0 0 12px 0;
  color: #5a6c7d;
  line-height: 1.6;
  font-size: 0.95em;
  word-break: break-word;
}

.task-status {
  display: inline-block;
  padding: 6px 12px;
  border-radius: 12px;
  font-size: 0.85em;
  font-weight: 600;
  background: #e3f2fd;
  color: #1976d2;
}

.task-item.completed .task-status {
  background: #e8f5e9;
  color: #388e3c;
}

.btn-delete {
  background: #ffebee;
  border: none;
  border-radius: 8px;
  width: 36px;
  height: 36px;
  cursor: pointer;
  font-size: 1.2em;
  transition: all 0.2s;
  flex-shrink: 0;
}

.btn-delete:hover {
  background: #f44336;
  transform: scale(1.1);
}

@media (max-width: 768px) {
  .task-header-left {
    flex-wrap: wrap;
  }
  
  .task-title {
    font-size: 1.1em;
  }
}
</style>
