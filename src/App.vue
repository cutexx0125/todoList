<template>
  <div id="app">
    <div class="todo-container">
      <div class="todo-wrap">
        
        <my-header @addTodo="addTodo"></my-header>

        <list :todoList="todoList"></list>
        
        <my-footer :todoList="todoList" @checkAllTodo="checkAllTodo" @clearAllTodo="clearAllTodo"></my-footer>
      </div>
    </div>
  </div>
</template>

<script>
import pubsub from 'pubsub-js'
import List from './components/List.vue'
import MyFooter from './components/MyFooter.vue'
import MyHeader from './components/MyHeader.vue'

export default {
  components: { MyHeader, List, MyFooter },
  name: 'App',
  data() {
    return {
      todoList: JSON.parse(localStorage.getItem('todoList')) || []
    }
  },
  methods: {
    // 添加一个todo
    addTodo(todo){
      this.todoList.unshift(todo)
    },
    // 勾选或取消勾选一个todo
    checkTodo(id) {
      this.todoList.forEach(todo => {
        if(todo.id === id) {
          todo.done = !todo.done
        }
      })
    },
    // 更新一个todo
    updateTodo(_, data) {
      let { id, title } = data
      this.todoList.forEach(todo => {
        if(todo.id === id) {
          todo.title = title
        }
      })
    },
    // 删除一个todo
    deleteTodo(_, id) {
      this.todoList = this.todoList.filter(todo => todo.id !== id)
    },
    // 全选或取消全选
    checkAllTodo(_, done) {
      this.todoList.forEach(todo => {
        todo.done = done
      })
    },
    clearAllTodo() {
      this.todoList = this.todoList.filter(todo => todo.done === false)
    }
  },
  watch: {
    todoList: {
      handler(value) {
        localStorage.setItem('todoList', JSON.stringify(value))
      },
      deep: true
    }
  },
  mounted() {
    this.pubId_check = pubsub.subscribe('checkTodo', this.checkTodo)
    this.pubId_delete = pubsub.subscribe('deleteTodo', this.deleteTodo)
    this.pubId_update = pubsub.subscribe('updateTodo', this.updateTodo)
  },
  beforeDestroy() {
    pubsub.unsubsribe(this.pubId_check)
    pubsub.unsubsribe(this.pubId_delete)
    pubsub.unsubsribe(this.pubId_update)
  },
}
</script>

<style>
/*base*/
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-edit {
  color: #fff;
  background-color: lightgreen;
  border: 1px solid green;
  margin-right: 5px;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn-edit:hover {
  color: #fff;
  background-color: green;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
