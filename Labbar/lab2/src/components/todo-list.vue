<script setup>
import {computed, onMounted, ref, watch} from "vue";

const todos = ref([]);
const name = ref('');
const sortAscending = ref(true);

const inputContent = ref('')
const inputCategory = ref(null)

const todosAsc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const todosDesc = computed(() => todos.value.slice().sort((a, b) => {
  return a.createdAt - b.createdAt
}))

const toggleSortOrder = () => {
  sortAscending.value = !sortAscending.value;
}
const sortedTodos = computed(() => {
  const sorted = sortAscending.value ? todosAsc.value : todosDesc.value;
  return [...sorted]
})

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos',JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

const addTodo = () => {
  if (inputContent.value.trim() === '' || inputCategory.value === null) {
    return
  }
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    createdAt: new Date().getTime()
  })

  inputContent.value = ''
  inputCategory.value = null

}

</script>

<template>
  <section class="create-todo">

    <h3>CREATE A TODO</h3>

    <form @submit.prevent="addTodo">

      <h4>What's on your todo list?</h4>
      <input type="text"
             placeholder="e.g vacuum the floors."
             v-model="inputContent">

      <h4>Pick a category</h4>
      <div class="options">

        <label>
          <input type="radio"
                 name="category"
                 value="business"
                 v-model="inputCategory">
          <span class="bubble business"></span>
          <div>Business</div>
        </label>

        <label>
          <input type="radio"
                 name="category"
                 value="personal"
                 v-model="inputCategory">
          <span class="bubble personal"></span>
          <div>Personal</div>
        </label>

      </div>
        <input type="submit" value="Add todo">
        <input type="submit" value="Sort TODOS" @click="toggleSortOrder">
    </form>
  </section>
  <section class="todo-list">
    <h3> TODO LIST</h3>

      <div v-for="todo in sortedTodos" :class="`todo-item ${todo.done && 'done'}`">

        <label>
          <input type="checkbox" v-model="todo.done">
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="todo-content">
          <input type="text" v-model="todo.content">
        </div>

        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Remove</button>
        </div>

      </div>
  </section>
</template>

<style scoped>

</style>