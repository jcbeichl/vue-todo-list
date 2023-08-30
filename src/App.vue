<script setup>
/*
 * ref = handles state
 * onMounted = used for localstorage
 * computed = used to order list
 * watch =  watch fro changes and make changes in local storage
 */
import { ref, onMounted, computed, watch } from "vue";

/* ref */
const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);

/* computed */
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

/* watch */
//setting name to localStorage
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

//setting todos to localStorage
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    //look through each array
    //if anything changes, runs fuction again
    deep: true,
  }
);

/* onMounted */
//gathering values from localStorage to display on page
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

/* functions */
//adds todo item to list
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

//remove button
//filter trhough each todo item
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
</script>

<template>
  <main class="app">
    <section class="greeting">
      <div class="title">
        Hello,
        <input type="text" id="name" placeholder="Name Here" v-model="name" />
      </div>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO ITEM</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. pay electric bill"
          v-model="input_content"
        />

        <h4>Pick a Category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble bubble-business"></span>
            <div class="bubble-label">Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble bubble-personal"></span>
            <div class="bubble-label">Personal</div>
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
              :class="`bubble ${
                todo.category === 'business'
                  ? 'bubble-business'
                  : 'bubble-personal'
              }`"
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
