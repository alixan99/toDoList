<template>
  <div class="todo-list">
    <h1>Список задач</h1>
    <input
    v-model="newTask"
    @keyup.enter="addTask"
    placeholder="Введите новую задачу" />

    <div class="filter-buttons">
      <button @click="filter = 'all'">Все</button>
      <button @click="filter = 'completed'">Выполненные</button>
      <button @click="filter = 'incomplete'">Невыполненные</button>
    </div>
    <ul>
      <li
      v-for="task in filteredTasks"
      :key="task.id">
      <TodoItem
      :task="task"
      @toggle-task="toggleTask"
      @remove-task="removeTask" />
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'
import TodoItem from '@/components/elements/TodoItem.vue'

export default {
  name: 'TaskList',
  components: {
    TodoItem
  },
  setup () {
    const newTask = ref('')
    const tasks = ref([])
    const filter = ref('all')

    const filteredTasks = computed(() => {
      if (filter.value === 'completed') {
        return tasks.value.filter(task => task.completed)
      } else if (filter.value === 'incomplete') {
        return tasks.value.filter(task => !task.completed)
      }
      return tasks.value
    })

    const addTask = () => {
      if (newTask.value.trim()) {
        tasks.value.push({ id: Date.now(), text: newTask.value, completed: false })
        newTask.value = ''
        saveTasks()
      }
    }

    const toggleTask = (taskId) => {
      const task = tasks.value.find(t => t.id === taskId)
      if (task) {
        task.completed = !task.completed
        saveTasks()
      }
    }

    const removeTask = (taskId) => {
      tasks.value = tasks.value.filter(task => task.id !== taskId)
      saveTasks()
    }

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    }

    const loadTasks = () => {
      const savedTasks = localStorage.getItem('tasks')
      if (savedTasks) {
        tasks.value = JSON.parse(savedTasks)
      }
    }

    onMounted(loadTasks)

    return {
      newTask,
      tasks,
      filter,
      filteredTasks,
      addTask,
      toggleTask,
      removeTask
    }
  }
}
</script>

<style lang="scss" scoped>
.todo-list {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: transparent; /* Белый фон для списка задач */
  padding: 20px;
  border-radius: 8px; /* Закругленные углы */
  // box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Тень */
  width: 400px; /* Ширина контейнера */
}

h1 {
  text-align: center; /* Заголовок по центру */
}

input {
  width: 100%; /* Полное использование ширины */
  padding: 10px;
  margin-bottom: 10px; /* Отступ между полем ввода и кнопками */
  border: 1px solid #ccc;
  border-radius: 4px;
}

.filter-buttons {
  display: flex;
  gap: 10px;
  justify-content: space-between; /* Распределить кнопки */
  margin-bottom: 10px; /* Отступ между кнопками и списком */
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 10px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3; /* Цвет при наведении */
}

ul {
  list-style-type: none; /* Удаление маркеров списка */
  padding: 0; /* Удаление отступов */
}

li {
  margin-bottom: 10px; /* Отступ между задачами */
}
</style>
