<template>
    <div>
        <div 
            class="col-md-12" 
            v-show="todos.length>0"
        >
            <h3>Todo Items</h3>
            <div
                v-for="(todo, index) in todos"
                class="row mrb-10"
                :key="index"
            >
                <div class="input-group m-b-5">
                    <span class="input-group-addon addon-right">
                        <input 
                            type="checkbox" 
                            v-model="todo.done"
                            :checked="todo.done" 
                            :value="todo.done"
                            v-on:change="updateTodo(todo)"
                            title="Mark as done?"
                        />
                    </span>

                    <input
                        type="text" 
                        class="form-control" 
                        :class="todo.done ? 'todo__done' : ''" 
                        v-model="todo.name"
                        @keypress="todo.editing=true" 
                        @keyup.enter="updateTodo(todo)"
                    >
                    <span class="input-group-addon addon-left" title="Delete todo?"
                          v-on:click="deleteTodo(todo._id)">X</span>
                </div>
                <span class="help-block small" v-show="todo.editing">Hit enter to update</span>
            </div>
        </div>

        <div 
            class="row alert alert-info text-center" 
            v-show="todos.length == 0"
        >
            <p class="alert alert-info">
                <strong>All Caught Up</strong>
                <br/>
                You do not have any todo items
            </p>
        </div>
    </div>
</template>

<script>
    
    import bus from './../../bus.js';
    import mongoApi from './../../api/mongoApi.js';

    export default {
        data() {
            return {
                todos: []
            }
        },
        created: function () {
            this.fetchTodo();
            this.listenToEvents();
        },
        methods: {
            fetchTodo() {
                mongoApi
                    .getAllTodo()
                        .then((response) => {
                            this.todos = response.data;
                        });
            },
            updateTodo(todo) {
                let id = todo._id;
                todo.editing = false;
                mongoApi
                    .updateTodo(id, todo)
                        .then((response) => {
                            console.log(response);
                        })
                        .catch((error) => {
                            console.log(error);
                        })
            },

            deleteTodo(id) {
                mongoApi
                    .deleteTodo(id)
                        .then((response) => {
                            console.log(response);
                        })
                        .catch((error) => {
                            console.log(error);
                        })
                this.fetchTodo();
            },

            listenToEvents() {
                bus.$on('refreshTodo', ($event) => {
                    this.fetchTodo(); //update todo
                })
            }
        }
    }
</script>

<style scoped>
    .todo__done {
        text-decoration: line-through !important
    }
    .no_border_left_right {
        border-left: 0px;
        border-right: 0px;
    }
    .flat_form {
        border-radius: 0px;
    }
    .mrb-10 {
        margin-bottom: 10px;
    }
    .addon-left {
        background-color: none !important;
        border-left: 0px !important;
        cursor: pointer !important;
    }
    .addon-right {
        background-color: none !important;
        border-right: 0px !important;
    }
</style>