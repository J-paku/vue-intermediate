<template>
  <div class="inputBox shadow">
    <input type="text" v-model="newTodoItem" v-on:keyup.enter="addTodo">
    <span class="addContainer" v-on:click="addTodo">
      <i class="addBtn fas fa-plus" aria-hidden="true"></i>
    </span>

      <ModalWarning v-if="showModal" @close="showModal = false">
        <h3 slot="header">
          えぽ！
          <span class="closeModalBtn" @click="showModal = false"><i class="fas fa-times"></i></span>
        </h3>
        <h3 slot="body">
          ちゃんと入力してからアップへやじ
        </h3>
      </ModalWarning>
  </div>
</template>

<script>
import ModalWarning from './common/Modal.vue'

export default {
  data: function () {
    return {
      newTodoItem: '',
      showModal: false
    }
  },
  methods: {
    addTodo: function () {
      if (this.newTodoItem !== '') {
        this.$emit('addItem', this.newTodoItem);
        this.clearInput();
      }
      else
      {
        this.showModal = !this.showModal;
      }
    },
    clearInput: function () {
      this.newTodoItem = '';
    }
  },
  components: {
    ModalWarning: ModalWarning
  }
}
</script>

<style scoped>
input:focus {
  outline: none;
}

.inputBox {
  display: flex;
  background: #F6F6F8;
  height: 50px;
  line-height: 50px;
  border-radius: 5px;
  justify-content: center;
  margin-bottom: .1rem;
}

.inputBox input {
  width: 90%;
  border-style: none;
  font-size: 0.9rem;
  text-align: center;
}

.addContainer {
  float: right;
  background: linear-gradient(to right, #6478FB, #8763FB);
  display: block;
  width: 3rem;
  border-radius: 0 5px 5px 0;
}

.addBtn {
  color: white;
  vertical-align: middle;
}
.closeModalBtn {
  color: #42b983;
  margin: 0 0 60% 60%;
  font-size: 3rem;
}

</style>