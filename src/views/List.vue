<template>
  <div id="list">
    <!-- 新增區 -->
    <b-container id="l-top">
      <b-row>
        <b-col col sm="8">
        <b-form-input v-model="newtodo"></b-form-input>
        </b-col>
      <b-btn variant="info" @click="addTodo">新增</b-btn>
      </b-row>
    </b-container>
    <b-table-simple class="text-center">
      <b-thead>
        <b-tr>
          <b-th>事項</b-th>
          <b-th>動作</b-th>
        </b-tr>
      </b-thead>
      <draggable v-model="todos" tag="tbody" v-bind="dragOption">
        <b-tr v-if="todos.length == 0">
          <b-td colspan="2">沒有資料</b-td>
        </b-tr>
        <b-tr v-else v-for="(todo, index) in todos" :key="index">
          <b-td>
            <b-form-input v-model="todo.model" v-if="todo.edit"></b-form-input>
            <b-btn variant="link" class="text-danger" v-if="todo.edit" @click="cancelTodo(index)">
              <font-awesome-icon :icon="['fas', 'undo']"></font-awesome-icon>
            </b-btn>
            <b-btn variant="link" class="text-success" v-if="todo.edit" @click="saveTodo(index)">
              <font-awesome-icon :icon="['fas', 'save']"></font-awesome-icon>
            </b-btn>
            <span v-else>{{ todo.name }}</span>
          </b-td>
          <b-td>
            <b-btn variant="link" class="text-primary" @click="editTodo(index)">
              <font-awesome-icon :icon="['fas', 'pen']"></font-awesome-icon>
            </b-btn>
            <b-btn variant="link" class="text-danger" @click="delTodo(index)">
              <font-awesome-icon :icon="['fas', 'times']"></font-awesome-icon>
            </b-btn>
          </b-td>
        </b-tr>
      </draggable>
    </b-table-simple>
  </div>
</template>

<script>
export default {
  data () {
    return {
      newtodo: '',
      dragOption: {
        animation: 200
      }
    }
  },
  methods: {
    addTodo () {
      this.$store.commit('addTodo', this.newtodo)
    },
    delTodo (index) {
      this.$store.commit('delTodo', index)
    },
    editTodo (index) {
      this.$store.commit('editTodo', index)
    },
    cancelTodo (index) {
      this.$store.commit('cancelTodo', index)
    },
    saveTodo (index) {
      this.$store.commit('saveTodo', index)
    }
  },
  computed: {
    todos: {
      get () {
        return this.$store.getters.todos
      },
      set (value) {
        this.$store.commit('dragTodo', value)
      }
    }
  }
}
</script>
