<template>
  <div id="app">
    <to-do-list msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import ToDoList from './components/ToDoList.vue'
export default {
  name: 'App',
  components: {
    'to-do-list':ToDoList,
  },
  data(){
    return {
      
    }
  }
}
</script>

