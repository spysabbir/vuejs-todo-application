<script setup>
import { ref, computed } from 'vue';

let todoList = ref([]);
let todo = ref(null);
let selectedTodo = ref(null);
let validationError = ref('');

const totalTodos = computed(() => todoList.value.length);
const completedTodos = computed(() => todoList.value.filter((todo) => todo.is_done).length);

const addTodo = () => {
    if (!todo.value) {
        validationError.value = 'Todo field is empty! Please enter your todo.';
        return;
    }

    const isTodoExist = todoList.value.some((t) => t.todo === todo.value);
    if (isTodoExist) {
        validationError.value = 'This todo already exists!';
        return;
    }

    let id = todoList.value.length;
    todoList.value.push({
        id: ++id,
        todo: todo.value,
        is_done: false,
    });

    todo.value = null;
    validationError.value = '';
};

const selectTodo = (row) => {
  selectedTodo.value = row;
  todo.value = row.todo;
};

const updateTodo = () => {
    if (!todo.value) {
        validationError.value = 'Todo field is empty! Please enter your todo.';
        return;
    }

    const isTodoExist = todoList.value.some((t) => t.todo === todo.value && t.id !== selectedTodo.value.id);
    if (isTodoExist) {
        validationError.value = 'This todo already exists!';
        return;
    }

    const index = todoList.value.findIndex((t) => t.id === selectedTodo.value.id);
    if (index !== -1) {
        todoList.value[index].todo = todo.value;
    }

    todo.value = selectedTodo.value = null;
    validationError.value = '';
};

const markAsDone = (row) => {
    if(row.is_done === false) {
        if(confirm(`Are you sure you want to mark ${row.todo} as done?`)) {
            let index = todoList.value.findIndex((t) => t.id === row.id);
            index !== -1 && (todoList.value[index].is_done = true);
        }
    }else{
        if(confirm(`Are you sure you want to mark ${row.todo} as not done?`)) {
            let index = todoList.value.findIndex((t) => t.id === row.id);
            index !== -1 && (todoList.value[index].is_done = false);
        }
    }
}

const deleteTodo = (row) => {
    if(confirm(`Are you sure you want to delete ${row.todo}?`)) {
        let index = todoList.value.findIndex((t) => t.id === row.id);
        index !== -1 && todoList.value.splice(index, 1);
    }
}

const deleteCompletedTodos = () => {
  if (confirm('Are you sure you want to delete all completed todos?')) {
    todoList.value = todoList.value.filter((todo) => !todo.is_done);
  }
};

const deleteAllTodos = () => {
  if (confirm('Are you sure you want to delete all todos?')) {
    todoList.value = [];
    todo.value = selectedTodo.value = null;
  }
};
</script>

<template>
<div>
    <div class="container">
        <div class="row">
            <div class="col-lg-12">
                <div class="card mt-2">
                <div class="card-header d-flex justify-content-between">
                    <h4 class="card-title">Create Todo</h4>
                    <div class="count">
                        <span class="badge bg-info">Total Todos: {{ totalTodos }}</span>
                        <span class="badge bg-success ms-2">Completed Todos: {{ completedTodos }}</span>
                    </div>
                </div>
                <div class="card-body">
                    <form @submit.prevent="!selectedTodo ? addTodo() : updateTodo()">
                        <div class="mt-2">
                            <label for="" class="form-label">Todo</label>
                            <div class="d-flex">
                                <input type="text" v-model="todo" class="form-control" id="todo" placeholder="Enter todo ..." />
                                <button v-if="!selectedTodo" class="btn btn-primary ms-3"><i class="bi bi-plus-circle-fill"></i></button>
                                <button v-else class="btn btn-primary ms-3"><i class="bi bi-pencil-square"></i></button>
                            </div>
                            <div v-if="validationError" class="text-danger">{{ validationError }}</div>
                        </div>
                    </form>
                </div>
            </div>
            <div class="card mt-2">
                <div class="card-header d-flex justify-content-between">
                    <h4 class="card-title">Todo List</h4>
                    <div class="action">
                        <button @click="deleteCompletedTodos" class="btn btn-info btn-sm">Delete Completed Todos</button>
                        <button @click="deleteAllTodos" class="btn btn-danger btn-sm ms-2">Delete All Todos</button>
                    </div>
                </div>
                <div class="card-body">
                    <div v-for="row in todoList" :key="row.id" class="toast show mt-2 w-100">
                        <div class="toast-header">
                            <strong class="me-auto"><del v-if="row.is_done">{{ row.todo }}</del><span v-else>{{ row.todo }}</span></strong>
                            <button class="btn btn-info btn-sm" @click="selectTodo(row)"><i class="bi bi-pencil-square"></i></button>
                            <button :class="`btn btn-${row.is_done ? 'success' : 'outline-secondary'} btn-sm ms-1`" @click="markAsDone(row)"><i class="bi bi-check-lg"></i></button>
                            <button class="btn btn-danger btn-sm ms-1" @click="deleteTodo(row)"><i class="bi bi-trash-fill"></i></button>
                        </div>
                    </div>
                    <div v-if="todoList.length === 0" class="alert alert-info mt-3">
                        The todo list is empty.
                    </div>
                </div>
            </div>
            </div>
        </div>
    </div>
</div>
</template>

<style scoped>

</style>
