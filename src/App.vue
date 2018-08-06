<template>
  <div id="app">
    <ToDoList></ToDoList>
  </div>
</template>

<script>

  import ToDoList from './components/todolist'

export default {
  name: 'app',
  components: {
    ToDoList
  }
}
</script>

<style>
@import './assets/style.css'

</style>
