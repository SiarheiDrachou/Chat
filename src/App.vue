<template>
  <div id = "app">
        <!-- Preloader  -->
        <preloader v-if = "isLoading"></preloader>

        <!-- TODO List -->
        <ul class="list-group container align-center pt-2" v-else>
            <todo-Item
                v-for = "(item, index) in todoItems" 
                :key  = "item.id"
                @create-item = "editNewTodo(index)"
            >
                <strong>{{item.login}}</strong>: {{item.text}}
            </todo-Item>
            <li class="list-group-item">
                <form @submit.prevent="addNewTodo">
                    <div class="form-group">
                      <label for="login">Login</label>
                      <input type="text" class="form-control" placeholder="Имя пользователя" v-model.trim="login" required/>
                    </div>
                    
                    <div class="form-group">
                      <label for="text">Example textarea</label>
                      <textarea class="form-control" rows="3" v-model.trim="newTodo" placeholder="comment" required></textarea>
                    </div>

                    <button type="submit" class="btn btn-outline-success" @click="users">Отправить</button>
                  </form>
            </li>
        </ul>
    </div>
</template>

<script>
import preloaderComponent from './components/Preloader.vue';
import todoItemComponent from './components/TodoItem.vue';

export default {
  components: {
        preloader: preloaderComponent,
        todoItem: todoItemComponent,
    },
    data() {
        return {
        isLoading: true,
        newTodo: '',
        login: '',
        todoItems: []
        }
    },
    created() {
        this.hidePreloader();
        this.loadUser();
    },
    methods: {
        addNewTodo() {
            if (!this.newTodo.length) return;

            this.todoItems.push({
                id        : this.todoItems.length + 1,
                text      : this.newTodo,
                login     : this.login
            });

            
            this.login = '';
            this.newTodo = '';
        },
        users() {
            const todoItems = {
                login: this.login,
                text: this.newTodo,
            }

            this.$http.post('http://localhost:3000/todoItems', todoItems)
                .then(response => {
                    return response.json();
                })
        },
        loadUser() {
            this.$http.get('http://localhost:3000/todoItems')
            .then(response => {
                    return response.json()
                })
                .then(todoItems => {
                    this.todoItems = todoItems;
                })
        },
        hidePreloader() {
            setTimeout(() => this.isLoading = false, 1000);
        },
    }
}
</script>