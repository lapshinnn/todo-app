<script setup>
import { ref, watch, onMounted } from 'vue'
import TaskItem from './components/TaskItem.vue'

// Загрузка задач из localStorage или использование начальных данных
const loadTasks = () => {
  const saved = localStorage.getItem('tasks')
  if (saved) {
    return JSON.parse(saved)
  }
  return [
    {
      id: Date.now(),
      title: 'Изучить Vue 3',
      text: 'Освоить основы Vue 3, включая Composition API, реактивность и работу с компонентами',
      completed: false
    },
    {
      id: Date.now() + 1,
      title: 'Создать приложение',
      text: 'Разработать полноценное приложение "Список дел" с использованием Vite и Vue',
      completed: false
    },
    {
      id: Date.now() + 2,
      title: 'Изучить TypeScript',
      text: 'Понять основы TypeScript для более надежной разработки на Vue',
      completed: false
    }
  ]
}

const tasks = ref(loadTasks())
const newTitle = ref('')
const newText = ref('')

// Сохранение в localStorage при изменении задач
watch(tasks, (newTasks) => {
  localStorage.setItem('tasks', JSON.stringify(newTasks))
}, { deep: true })

// Добавление новой задачи
const addTask = () => {
  if (newTitle.value.trim() === '') {
    alert('Введите название задачи!')
    return
  }
  
  tasks.value.push({
    id: Date.now(),
    title: newTitle.value.trim(),
    text: newText.value.trim(),
    completed: false
  })
  
  newTitle.value = ''
  newText.value = ''
}

// Удаление задачи
const deleteTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id)
}

// Переключение статуса выполнения
const toggleComplete = (id) => {
  const task = tasks.value.find(t => t.id === id)
  if (task) {
    task.completed = !task.completed
  }
}

// Подсчет выполненных и невыполненных задач
const completedCount = ref(0)
const activeCount = ref(0)

watch(tasks, (newTasks) => {
  completedCount.value = newTasks.filter(t => t.completed).length
  activeCount.value = newTasks.filter(t => !t.completed).length
}, { immediate: true, deep: true })
</script>

<template>
  <div class="app">
    <header class="app-header">
      <h1>📝 Мой список дел</h1>
      <div class="stats">
        <span class="stat">Всего: {{ tasks.length }}</span>
        <span class="stat active">Активных: {{ activeCount }}</span>
        <span class="stat completed">Выполнено: {{ completedCount }}</span>
      </div>
    </header>

    <!-- Форма добавления новой задачи -->
    <div class="add-task-form">
      <h2>➕ Добавить новую задачу</h2>
      <div class="form-group">
        <input 
          v-model="newTitle" 
          type="text" 
          placeholder="Название задачи"
          @keyup.enter="addTask"
          class="input-title"
        />
        <textarea 
          v-model="newText" 
          placeholder="Описание задачи (необязательно)"
          rows="3"
          class="input-text"
        ></textarea>
        <button @click="addTask" class="btn-add">
          Добавить задачу
        </button>
      </div>
    </div>

    <main class="tasks-container">
      <TaskItem 
        v-for="task in tasks" 
        :key="task.id"
        :task="task"
        @delete="deleteTask"
        @toggle-complete="toggleComplete"
      />
      
      <div v-if="tasks.length === 0" class="empty-state">
        <p>📭 Список задач пуст</p>
        <p class="empty-hint">Добавьте свою первую задачу выше</p>
      </div>
    </main>
  </div>
</template>

<style scoped>
.app {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
}

.app-header {
  text-align: center;
  margin-bottom: 30px;
  padding: 30px 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 20px;
  color: white;
  box-shadow: 0 10px 30px rgba(102, 126, 234, 0.3);
}

.app-header h1 {
  margin: 0 0 15px 0;
  font-size: 2.5em;
  font-weight: 700;
}

.stats {
  display: flex;
  justify-content: center;
  gap: 20px;
  flex-wrap: wrap;
}

.stat {
  background: rgba(255, 255, 255, 0.2);
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 0.95em;
  font-weight: 600;
}

.stat.active {
  background: rgba(255, 193, 7, 0.3);
}

.stat.completed {
  background: rgba(76, 175, 80, 0.3);
}

/* Форма добавления задачи */
.add-task-form {
  background: white;
  padding: 25px;
  border-radius: 15px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  margin-bottom: 30px;
}

.add-task-form h2 {
  margin: 0 0 20px 0;
  color: #2c3e50;
  font-size: 1.5em;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.input-title,
.input-text {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid #e0e0e0;
  border-radius: 10px;
  font-size: 1em;
  font-family: inherit;
  transition: border-color 0.3s;
}

.input-title:focus,
.input-text:focus {
  outline: none;
  border-color: #667eea;
}

.input-text {
  resize: vertical;
  min-height: 80px;
}

.btn-add {
  padding: 12px 24px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 10px;
  font-size: 1em;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.btn-add:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
}

.btn-add:active {
  transform: translateY(0);
}

/* Контейнер задач */
.tasks-container {
  display: grid;
  gap: 20px;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
}

/* Пустое состояние */
.empty-state {
  grid-column: 1 / -1;
  text-align: center;
  padding: 60px 20px;
  color: #9e9e9e;
}

.empty-state p {
  font-size: 1.5em;
  margin: 0 0 10px 0;
}

.empty-hint {
  font-size: 1em !important;
  color: #bdbdbd;
}

@media (max-width: 768px) {
  .app-header h1 {
    font-size: 2em;
  }
  
  .stats {
    gap: 10px;
  }
  
  .tasks-container {
    grid-template-columns: 1fr;
  }
}
</style>
