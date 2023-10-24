<template>
  <div id="app">
    <TodoHeader></TodoHeader>
    <TodoInput v-on:addItem="addOneItem"></TodoInput>
    <TodoList v-bind:propsdata="todoItems" v-on:removeItem="removeOneItem" v-on:toggleItem="toggleOneItem"></TodoList>
    <TodoFooter v-on:clearAll="clearAllItems"></TodoFooter>
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader.vue'
import TodoInput from './components/TodoInput.vue'
import TodoList from './components/TodoList.vue'
import TodoFooter from './components/TodoFooter.vue'

export default {
  data: function () {
    return {
      todoItems: []
    }
  },
  methods: {
    addOneItem: function (todoItem) {
      var obj = { completed: false, item: todoItem };
      localStorage.setItem(todoItem, JSON.stringify(obj));
      this.todoItems.push(obj);
    },
    removeOneItem: function (todoItem, index) {
      this.todoItems.splice(index, 1);
      localStorage.removeItem(todoItem.item);
    },
    toggleOneItem: function (todoItem) {
      todoItem.completed = !todoItem.completed;
      localStorage.removeItem(todoItem.item);
      localStorage.setItem(todoItem.item, JSON.stringify(todoItem));
    },
    clearAllItems: function () {
      this.todoItems = [];
      localStorage.clear();
    }
  },
  created: function () {
    if (localStorage.length > 0) {
      for (var i = 0; i < localStorage.length; i++) {
        if (localStorage.key(i) !== 'loglevel:webpack-dev-server') {
          this.todoItems.push(JSON.parse(localStorage.getItem(localStorage.key(i))));
        }
      }
    }
  },
  components: {
    TodoHeader: TodoHeader,
    TodoInput: TodoInput,
    TodoList: TodoList,
    TodoFooter: TodoFooter
  }
}
</script>

<style>
html {
  max-width: 80%;
  margin : 0 auto;
  font-family: 'Noto Sans JP', sans-serif;
  font-family: 'Open Sans', sans-serif;
  font-family: 'Poppins', sans-serif;
  font-family: 'Ubuntu', sans-serif;
}
body {
  text-align: center;
  background-color: #F6F6F8;
}

input {
  border-style: groove;
  width: 200px;
}

button {
  border-style: groove;
}
</style>