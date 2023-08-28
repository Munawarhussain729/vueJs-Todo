<script setup>
import { onMounted, watch, computed, ref } from "vue";

const name = ref("");
const todos = ref([]);

const new_todo_content = ref("");
const todo_category = ref(null);

const todo_asc = computed(() =>
  todos.value.sort((a, b) => b.createdAt - a.createdAt)
);

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
const addToDo = () => {
  if (new_todo_content.value.trim() === "" || new_todo_content.value == null) {
    return;
  }
  todos.value.push({
    content: new_todo_content.value,
    category: todo_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        what's up <input type="text" placeholder="Name here " v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a ToDo</h3>
      <form @submit.prevent="addToDo">
        <h4>What's on your todo list</h4>
        <input
          type="text"
          placeholder="e.g attend meeting"
          v-model="new_todo_content"
        />
        <h4>Pick a Cateogry</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="cateogry"
              value="personal"
              v-model="todo_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          <label>
            <input
              type="radio"
              name="cateogry"
              value="business"
              v-model="todo_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
        </div>

        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>ToDo List</h3>
      <div class="list">
        <div
          v-for="todo in todo_asc"
          :key="todo"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
