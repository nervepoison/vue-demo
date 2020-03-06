<template>
  <div id="app">
    <section class="todoapp">
      <header class="header">
        <h1>todos</h1>
        <input
          type="text"
          class="new-todo"
          v-autofocus
          placeholder="接下来你要做什么？"
          v-model="newTodo"
          @keyup.enter="addTodo"
        >
      </header>
      <section
        class="main"
        v-show="todos.length"
      >
        <!-- <ul class="todo-list"> -->
        <transition-group
          name="staggered-fade"
          tag="ul"
          v-bind:css="false"
          v-on:before-enter="beforeEnter"
          v-on:enter="enter"
          v-on:leave="leave"
          class="todo-list"
        >
          <li
            v-for="todo in computedTodos"
            class="todo"
            :key="todo.id"
            :class="{ completed:todo.completed}"
          >
            <todo-item
              :title.sync="todo.title"
              :completed.sync="todo.completed"
              @delete="removeTodo(todo)"
            ></todo-item>

          </li>
        </transition-group>
        <!-- </ul> -->
        <footer
          class="footer"
          v-show="todos.length"
        >
          <span class="todo-count">
            <!-- remaining 计算剩余的未完成的数量，pluralize 用来过滤单位是否要负数 -->
            <strong>{{ remaining }}</strong> {{ remaining | pluralize }} 未完成
          </span>
          <!-- 当有已完成的备忘时，一键移除已完成按钮出现 -->
          <button
            class="clear-completed"
            @click="removeCompleted"
            v-show="todos.length > remaining"
          >
            清空已完成的todo
          </button>
        </footer>
      </section>
    </section>
  </div>
</template>

<script>
import TodoItem from "../src/components/TodoItem";
import Velocity from 'velocity-animate'

let id = 1;
export default {
  components: {
    "todo-item": TodoItem
  },

  name: "App",
  data() {
    return {
      todos: [],
      newTodo: ""
    };
  },

  //自定义指令，自动聚焦
  directives: {
    autofocus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },

  computed: {
    // 计算剩余未完成的备忘
    remaining() {
      // 过滤掉已完成的，获取数量
      return this.todos.filter(x => !x.completed).length;
    },
    //计算匹配的备忘
    computedTodos() {
      return this.todos.filter(item => {
        return (
          item.title.toLowerCase().indexOf(this.newTodo.toLowerCase()) !== -1
        );
      });
    }
  },
  filters: {
    // 计算单位
    pluralize(num) {
      // 如果是多个，则加复数
      return num > 1 ? "items" : "item";
    }
  },
  methods: {
    addTodo() {
      if (!this.newTodo) {
        return;
      }

      this.todos.unshift({
        id: id++,
        title: this.newTodo,
        completed: false
      });
      this.newTodo = "";
    },
    removeTodo(todo) {
      const index = this.todos.findIndex(x => x.id == todo.id);
      this.todos.splice(index, 1);
    },
    removeCompleted() {
      this.todos = this.todos.filter(x => !x.completed);
    },

    /* 动画 */
    //进入中
    beforeEnter(el) {
      el.style.opacity = 0;
      el.style.height = 0;
    },
    enter(el, done) {
      var delay = el.dataset.index * 150;
      setTimeout(function() {
        // eslint-disable-next-line
        Velocity(el, { opacity: 1, height: "58px" }, { complete: done });
      }, delay);
    },
    leave(el, done) {
      var delay = el.dataset.index * 150;
      setTimeout(function() {
        // eslint-disable-next-line
        Velocity(el, { opacity: 0, height: 0 }, { complete: done });
      }, delay);
    }
  }
};
</script>

<style>
@import "https://unpkg.com/todomvc-app-css@2.1.0/index.css";
</style>
