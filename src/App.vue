<script setup>
    import { ref, computed, onMounted, watch } from "vue";
    const todos = ref([]);
    const name = ref("");

    const title = ref("");
    const category = ref("");

    const error = ref(false);

    const todo_asc = computed(() => {
        return todos.value.sort((a, b) => b.createdAt - a.createdAt);
    });

    const addTodo = () => {
        if (!title.value.trim() || !category.value) {
            error.value = true;
            return;
        }
    
        todos.value.push({
            title: title.value,
            category: category.value,
            done: false,
            createdAt: Date.now(),
        });

        title.value = "";
        category.value = "";
    };

    const removeTodo = (todo) => {
        const index = todos.value.indexOf(todo);
        todos.value.splice(index, 1);
    };

    watch(todos, (newVal) => {
        localStorage.setItem("todos", JSON.stringify(newVal));
    }, { deep: true });

    watch(name, (newVal) => {
        localStorage.setItem("name", newVal);
    });

    onMounted(() => {
        name.value = localStorage.getItem("name") || "";
        const savedTodos = localStorage.getItem("todos");
        if (savedTodos) {
            todos.value = JSON.parse(savedTodos);
        }
    });
</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                What's up, <input type="text" placeholder="Someone?" v-model="name"/>
            </h2>
        </section>

        <section class="create-todo">
            <h3>CREATE A TODO</h3>

            <form @submit.prevent="addTodo">
                <h4>What's on your todo list?</h4>
                <input type="text" v-model="title" placeholder="e.g. Learn Vuejs"/>
                <h4>Pick a category</h4>
                <div class="options">
                    <label>
                        <input type="radio" v-model="category" value="business" name="category"/>
                        <span class="bubble business"></span>
                        <div>Business</div>
                    </label>
                    <label>
                        <input type="radio" v-model="category" value="personal" name="category"/>
                        <span class="bubble personal"></span>
                        <div>Personal</div>
                    </label>
                </div>
                <div class="form-error" v-if="error">Please fullfilling information!</div>
                <input type="submit" value="Add Todo"></input>
            </form>
        </section>
        <section class="todo-list">
            <h3>TODO LIST</h3>
            <div v-if="todos.length === 0" class="empty">No todos yet!</div>
            <div v-for="todo in todo_asc" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
                <label>
                    <input type="checkbox" v-model="todo.done"/>
                    <span :class="`bubble ${todo.category}`"></span>
                </label>
                <div class="todo-content">
                    <input type="text" v-model="todo.title" />
                </div>
                <div class="actions">
                    <button class="delete" @click="removeTodo(todo)">Delete</button>
                </div>
            </div>
        </section>
    </main>
</template>