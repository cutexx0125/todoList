<template>
  <transition name="todo" appear>
    <li>
    <label>
      <input type="checkbox" :checked="todo.done" @change="handleCheck(todo.id)"/>
      <span v-show="!todo.isEdit">{{todo.title}}</span>
      <input type="text" :value="todo.title" v-show="todo.isEdit" 
      @blur="handleBlur(todo, $event)" ref="inputTitle">
    </label>
    <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
    <button class="btn btn-edit" @click="handleEdit(todo)" v-show="!todo.isEdit">编辑</button>
  </li>
  </transition>
</template>

<script>
import pubsub from 'pubsub-js'

export default {
  name: 'Item',
  props: ['todo'],
  methods: {
    //勾选或取消勾选
    handleCheck(id) {
      // 通知App组件将对应todo的done取反
      pubsub.publish('checkTodo', id)
    },
    handleDelete(id) {
      if(confirm('确定删除吗？')){
        // 通知App组件删除对应的todo
        pubsub.publish('deleteTodo', id)
      }
    },
    handleEdit(todo) {
      if(todo.hasOwnProperty('isEidt')) {
        todo.isEdit = true
      }else {
        this.$set(todo, 'isEdit', true)
      }
      this.$nextTick(() => {
        this.$refs.inputTitle.focus()
      })
    },
    handleBlur(todo, e) {
      let id = todo.id
      let title = e.target.value
      todo.isEdit = false
      if(!e.target.value.trim()) return alert('输入不能为空')
      pubsub.publish('updateTodo', {id, title} )
    }
  },
}
</script>

<style scoped>
/*item*/
li {
  list-style: none;
  height: 36px;
  line-height: 36px;
  padding: 0 5px;
  border-bottom: 1px solid #ddd;
}

li label {
  float: left;
  cursor: pointer;
}

li label li input {
  vertical-align: middle;
  margin-right: 6px;
  position: relative;
  top: -1px;
}

li button {
  float: right;
  display: none;
  margin-top: 3px;
}

li:before {
  content: initial;
}

li:last-child {
  border-bottom: none;
}

li:hover{
  background-color: #ddd;
}

li:hover button{
  display: block;
}

.todo-enter-active{
  animation: todo 0.5s linear;
}

.todo-leave-active{
  animation: todo 0.5s linear reverse;
}

@keyframes todo {
  from{
    transform: translateX(100%);
  }
  to{
    transform: translateX(0px);
  }
}
</style>