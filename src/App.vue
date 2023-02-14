<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_task = ref("");
const input_category = ref(null);

// Sorts items in ascending order by creation date
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_task.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_task.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  resetForm();
};

const resetForm = () => {
  input_task.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

const ObtainCurrentDate = () => {
  const date = new Date();
  const day = date.getDate();
  const dayName = date.toLocaleDateString("default", { weekday: "long" });
  const monthName = date.toLocaleString("default", { month: "long" });
  return day + " " + monthName + ", " + dayName;
};

const currentDate = ObtainCurrentDate();
watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hey <input type="text" placeholder="Name Here" v-model="name" />
      </h2>
      <h2>{{ currentDate }}</h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TASK</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. Water flowers"
          v-model="input_task"
        />

        <h4>Select a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="work"
              v-model="input_category"
            />
            <span class="bubble work"></span>
            <div>Work</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${todo.category == 'work' ? 'work' : 'personal'}`"
            ></span>
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

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
