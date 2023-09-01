<script setup>
import {ref} from 'vue';

let todoList = ref([]);
let todo = ref(null);
let selectedTodo = ref(null);

const addTodo = () => {
    if(validateTodo(todo.value)){
        let id = todoList.value.length;
        todoList.value.push({
            id: ++id,
            todo: todo.value,
            is_done: false
        })
        todo.value = null
    }
}

const selectTodo = (row) => {
    selectedTodo.value = row;
    todo.value = row.todo;
}

const updateTodo = () => {
    if(validateTodo(todo.value)){
        let index = todoList.value.findIndex((t) => t.id === selectedTodo.value.id);
        index !== -1 && (todoList.value[index].todo = todo.value);
        todo.value = selectedTodo.value = null;
    }
}

const markAsDone = (row) => {
    if(confirm(`Are you sure you want to mark ${row.todo} as done?`)) {
        let index = todoList.value.findIndex((t) => t.id === row.id);
        index !== -1 && (todoList.value[index].is_done = true);
    }
}

const deleteTodo = (row) => {
    if(confirm(`Are you sure you want to delete ${row.todo}?`)) {
        let index = todoList.value.findIndex((t) => t.id === row.id);
        index !== -1 && todoList.value.splice(index, 1);
    }
}

const validateTodo = (new_todo) => {
    let index = todoList.value.findIndex((t) => t.todo === new_todo);
    if(index !== -1){
        alert('Todo already exist!');
        return false;
    }else{
        return true;
    }
}
</script>

<template>
    <div>
        <div class="d-flex justify-content-center container">
            <div class="w-75">
                <form @submit.prevent="!selectedTodo ? addTodo() : updateTodo()">
                    <div class="mb3 mt-5">
                        <label for="todo">What's your todo?</label>
                        <div class="d-flex">
                            <input type="text" v-model="todo" class="form-control" id="todo" placeholder="Enter todo name.">
                            <button v-if="!selectedTodo" class="btn btn-primary ms-3"><i class="bi bi-plus-circle-fill"></i></button>
                            <button v-else class="btn btn-primary ms-3"><i class="bi bi-pencil-square"></i></button>
                        </div>
                    </div>
                </form>
                <div @click="selectTodo(row)" v-for="row in todoList" :key="row.id" class="toast show mt-2 w-100">
                    <div class="toast-header">
                        <strong class="me-auto">{{ row.todo }}</strong>
                        <button :class="`btn btn-${row.is_done ? 'success' : 'outline-secondary'} btn-sm`" @click.stop="markAsDone(row)"><i class="bi bi-check-lg"></i></button>
                        <button class="btn btn-danger btn-sm ms-1" @click.stop="deleteTodo(row)"><i class="bi bi-trash-fill"></i></button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>

</style>
