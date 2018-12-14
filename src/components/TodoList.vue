<template>
  <div>
    <input type="text" class="todo-input" placeholder="What needs
    to be done" v-model="newTodo" @keyup.enter="addTodo">
    <transition-group enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
        <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
            <div class="todo-item-left">
                <input type="checkbox" v-model="todo.completed">
                <div v-if="!todo.editing" class="todo-item-label" @dblclick="editTodo(todo)" :class="{completed: todo.completed}">
                    {{todo.title}}
                </div>
                <input v-else type="text" class="todo-item-edit" v-model="todo.title" 
                @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)">
            </div>
            <div class="remove-item" @click="removeTodo(index)">&times;</div>
        </div>
    </transition-group>
    <div class="extra-container">
        <div>
            <label>
                <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All
            </label>
        </div>
        <div>{{remaining}} item left</div>
    </div>
    <div class="extra-container">
        <div>
            <button :class="{active: filter == 'all'}" @click="filter = 'all'">All</button>
            <button :class="{active: filter == 'active'}" @click="filter = 'active'">Active</button>
            <button :class="{active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
        </div>

        <div>
            <button v-if="showClearBtn" @click="clearCompleted">Clear Completed</button>
        </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "todo-list",
  data() {
    return {
        newTodo: '',
        idForTodo: 3,
        beforeEditTile: '',
        filter: 'all',
        todos: [
            {
                id: 1,
                title: 'Finish vue screencast', 
                completed: false,
                editing: false
            },
            {
                id: 2,
                title: 'Take over world', 
                completed: false,
                editing: false
            }
        ]
    }
  },
  computed: {
      remaining () {
          return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining () {
          return this.remaining != 0
      },
      todosFiltered () {
          if (this.filter == 'all') {
              return this.todos
          }
          else if (this.filter == 'active') {
              return this.todos.filter(todo => !todo.completed)
          }
          else if (this.filter == 'completed') {
              return this.todos.filter(todo => todo.completed)
          }
      },
      showClearBtn () {
          return this.todos.filter(todo => todo.completed).length > 0
      }
  },
  methods: {
      addTodo () {
          if (this.newTodo.trim().length == 0) {
            return;
        } 
          this.todos.push({
              id: this.idForTodo,
              title: this.newTodo,
              completed: false
          })
          this.newTodo = ''
          this.idForTodo ++
      },
      editTodo (todo) {
          this.beforeEditTitle = todo.title
          todo.editing = true
      },
      doneEdit (todo) {
          if (todo.title.trim() == '') {
              todo.title = this.beforeEditTitle
          }
          todo.editing = false
      },
      removeTodo(index) {
          this.todos.splice(index, 1)
      },
      checkAllTodos () {
          this.todos.forEach((todo) => todo.completed = event.target.checked)
      },
      clearCompleted () {
          this.todos = this.todos.filter(todo => !todo.completed)
      }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
    @import url('https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css');

    .todo-input {
        width: 95%;
        padding: 10px 18px;
        font-size: 14px;
        margin-bottom: 15px;

        &:focus {
            outline: 0;
        }
    }
    .todo-item {
        margin-bottom: 15px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        animation-duration: 0.3s;
    }
    .remove-item {
        margin-left: 14px;
        cursor: pointer;

        &:hover {
            color: red;
        }
    }
    .todo-item-left {
        display: flex;
        align-items: center;
    }
    .todo-item-label {
        padding: 10px;
        border: 1px solid white;
        margin-left: 12px;
    }
    .todo-item-edit {
        font-size: 24px;
        color: #2c3e50;
        margin-left: 12px;
        width: 95%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Arial, Helvetica, sans-serif;

        &:focus {
            outline: 0;
        }
    }
    .completed {
        text-decoration: line-through;
        color: grey;
    }
    .extra-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgray;
        padding-top: 14px;
        margin-bottom: 14px;
    }
    button {
        font-size: 14px;
        appearance: none;
        background-color: white;

        &:hover {
            background-color: lightgreen;
        }
        &:focus {
            outline: none;
        }
    }
    .active {
        background-color: lightgreen;
    }
</style>
