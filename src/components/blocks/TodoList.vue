<template>
  <div class="todo">
    <h1 class="todo__title">Список задач</h1>
    <input class="todo__input"
    v-model="newTask"
    @keyup.enter="addTask"
    placeholder="Введите новую задачу" />

    <div class="filter-buttons">
      <button @click="filter = 'all'">Все</button>
      <button @click="filter = 'completed'">Выполненные</button>
      <button @click="filter = 'incomplete'">Невыполненные</button>
    </div>
    <ul class="todo-list">
      <li
      class="todo-list__item"
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
    const tasks = ref([]) // массив который содержит все задачи
    const filter = ref('all') // переменная которая отвечает за фильтрацию (по умолчанию выводит ВСЕ задачи)

    // при каждом нажатии на фильтр, меняется значение filter.value? а затем возвращается новый массив, где у объектов совпадают значение task.completed и filter.value (true\false)
    const filteredTasks = computed(() => {
      if (filter.value === 'completed') {
        return tasks.value.filter(task => task.completed)
      } else if (filter.value === 'incomplete') {
        return tasks.value.filter(task => !task.completed)
      }
      return tasks.value
    })

    // нажатие Enter провоцирует событие addTask, которое добавляет в конец массива tasks новую задачу, затем очищает поле ввода в input и вызывает функцию обновления данных в localStorage
    const addTask = () => {
      if (newTask.value.trim()) {
        tasks.value.push({ id: Date.now(), text: newTask.value, completed: false })
        newTask.value = ''
        saveTasks()
      }
    }

    // при изменении статуса задачи, меняется значение в объекте на противоположное (true\false) и так же вызывается функция обновления данных в localStorage
    const toggleTask = (taskId) => {
      const task = tasks.value.find(t => t.id === taskId)
      if (task) {
        task.completed = !task.completed
        saveTasks()
      }
    }

    // при удалении задачи, передается ее ID и функция фильтрует весь массив, а так же обновляет данные в локальном хранилище
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

    // после монтирования компонента TodoList, вызывается коллбэк на выгрузку массива из локального хранилища
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
.todo {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: transparent; /* Белый фон для списка задач */
  padding: 20px;
  border-radius: 8px; /* Закругленные углы */
  // box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Тень */
  max-width: 400px; /* Ширина контейнера */
  &__title {
    text-align: center; /* Заголовок по центру */
  }
  &__input {
    width: 100%; /* Полное использование ширины */
    padding: 10px;
    margin-bottom: 10px; /* Отступ между полем ввода и кнопками */
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  &-list {
    list-style-type: none; /* Удаление маркеров списка */
    padding: 0; /* Удаление отступов */
    &__item {
      margin-bottom: 10px; /* Отступ между задачами */
    }
  }
}

.filter-buttons {
  display: flex;
  gap: 10px;
  justify-content: space-between;
  margin-bottom: 10px;
  button {
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    padding: 10px;
    cursor: pointer;
    &:hover {
      background-color: #0056b3; /* Цвет при наведении */
    }
  }
}

</style>
