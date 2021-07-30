<template>
  <div class="todo-footer" v-show="total">
    <label>
      <input type="checkbox" :checked="isAll" @change="checkAll"/>
    </label>
    <span>
      <span>已完成{{doneTodo}}</span> / 全部{{total}}
    </span>
    <button class="btn btn-danger" @click="clearAll">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: 'MyFooter',
  props: ['todoList'],
  computed:{
    total() {
      return this.todoList.length
    },
    doneTodo() {
      return this.todoList.filter(todo => todo.done === true).length
    },
    isAll() {
      return this.doneTodo === this.total && this.total > 0
    }
  },
  methods: {
    checkAll(e) {
      this.$emit('checkAllTodo', e.target.checked)
    },
    clearAll() {
      if(confirm("确定清除吗？")){
        this.$emit('clearAllTodo')
      }  
    }
  }
}
</script>

<style scoped>
/*footer*/
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>