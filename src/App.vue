<template>
  <div class="app-container" id="app">
    <TodoList />
  </div>
</template>

<script>
import TodoList from '@/components/blocks/TodoList.vue'

export default {
  name: 'App',
  components: {
    TodoList
  }
}
</script>

<style lang="scss">
.app-container {
  background-image: url('@/assets/image/background.jpg');/* подходящий фон */
  background-size: no-repeat;
  background-position: center;
  background-size: cover;
  height: 100vh;
  font-family: 'Montserrat-Regular', sans-serif;
  display: flex;
  justify-content: center;
  align-items: start;
  height: 100vh; /* на всю высоту экрана */
  background-color: #f4f4f9;
}
</style>
