<template>
  <div :class="{editing:isEdited}">
    <div class="view">
      <input
        type="checkbox"
        class="toggle"
        @change="updateChecked($event.target.checked)"
      >
      <label @dblclick="editTodo(todo)">{{title}}</label>
      <button
        class="destroy"
        @click="removeTodo(todo)"
      ></button>
    </div>
    <input
      type="text"
      class="edit"
      v-autofocus
      v-if="isEdited"
      v-model="editingTitle"
      @blur="doneEdit()"
      @keyup.enter="doneEdit()"
      @keyup.esc="cancelEdit()"
    >

  </div>
</template>

<script>
export default {
  name: "TodoItem",
  data() {
    return {
      isEdited: false,
      editingTitle: ""
    };
  },
  props: {
    title: {
      type: String,
      default: ""
    },
    completed: {
      type: Boolean,
      default: true
    }
  },
  methods: {
    //编辑
    editTodo() {
      this.editingTitle = this.title;
      this.isEdited = true;
    },
    //确认修改
    doneEdit() {
      // ESC 也会触发 blur 事件，需要判断原有状态是否是编辑中
      if (this.isEdited) {
        //更新绑定的title
        this.$emit("update:title", this.editingTitle);
        this.isEdited = false;
      }
    },
    cancelEdit() {
      this.isEdited = false;
    },
    updateChecked(completed) {
      this.$emit("update:completed", completed);
    },
    removeTodo() {
      this.$emit("delete");
    }
  }
};
</script>

<style>
 /* 由于改动了原有样式结构，所以需要改变对应的 CSS 选择器的结构 */
  .todo-list li .editing .view {
    display: none;
  }
  .todo-list li .editing .edit {
    display: block;
    width: 506px;
    padding: 12px 16px;
    margin: 0 0 0 43px;
  }
</style>