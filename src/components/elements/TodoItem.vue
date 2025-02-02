<template>
  <div lass="todo-item">
    <input type="checkbox"
    :checked="task.completed"
    @change="toggleTask" />
    <span
    :style="{ textDecoration: task.completed ? 'line-through' : '' }">{{ task.text }}</span>
    <button @click="removeTask">Удалить</button>
  </div>
</template>

<script>
export default {
  name: 'TodoItem',
  props: ['task'],
  emits: ['toggle-task', 'remove-task'],
  setup (props, { emit }) {
    const toggleTask = () => emit('toggle-task', props.task.id)
    const removeTask = () => emit('remove-task', props.task.id)

    return {
      toggleTask,
      removeTask
    }
  }
}
</script>

<style lang="scss" scoped>
.todo-item {
  display: flex;
  align-items: center; /* Центрирование элементов в строке */
  justify-content: space-between; /* Распределение элементов в строке */
  padding: 10px;
  background: #f9f9f9; /* Светлый фон для задач */
  border-radius: 4px; /* Закругленные углы */
}

span {
  flex-grow: 1; /* Занять оставшееся пространство */
  margin-left: 10px; /* Отступ между чекбоксом и текстом */
}

button {
  background-color: #dc3545; /* Красный фон для кнопки удаления */
  color: white;
  border: none;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
}

button:hover {
  background-color: #c82333; /* Цвет при наведении на кнопку удаления */
}
</style>
