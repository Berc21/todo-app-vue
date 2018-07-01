<template>
  <div class="todo-container">
    <form  class="todo-form" @submit.prevent="addTodo" >
      <input class="todo-input" type="text" @keyup.enter="addTodo" v-model="newTodoItem" placeholder="add new todo">
      <button class="todo-button" type="submit">Add</button>
    </form>
    <ul class="todo-list">
      <transition-group name="fade">

      <li @dblclick="showEdit(item)" class="todo-list-item" v-for="(item , id) in list" :key="id" @mouseover="showById = id" @mouseout="showById = null" >

        <input class="todo-done"  @click="selectShift(id, $event)" v-model="item.isDone" type="checkbox" >
        <div class="todo-text" v-if="item.isEditing">
               <input class="todo-edit" type="text" @keyup.enter="editTodo(item)"  @blur="editTodo(item)" @keyup.esc="cancelEdit(item)" v-model="item.text"  v-focus >
               <button class="todo-cancel-button" @click="cancelEdit(item)">Cancel</button>
        </div>
          <span class="todo-text" :class="{ 'is-done': item.isDone }" v-else>
                {{ item.text }}
          </span>
          <span v-show="showById == id" @click="removeItem(id)" class="todo-list-delete">X</span>
        </li>
         </transition-group>
    </ul>


   <div class="todo-helpers-container">
    <div v-if="!isEmpty">
      <label v-if="allDone" >
         <input type="checkbox"  checked @change="unCheckAllList"> Uncheck all <br>
      </label>
        <label  v-else >
         <input type="checkbox"  @change="checkAllList" > Check All <br>
      </label>
    </div>

  <transition name="fade">

    <button class="todo-clear-done-button" v-show="showClearDone" @click="clearDone"> Clear Done</button>

  </transition>
    <p  class="todo-left" v-if="todoLeft > 0" >{{todoLeft}} Todo Left  </p>
    <p  class="todo-left" v-else > All is done - You're free! </p>
   </div>

  </div>
</template>

<script>
import isEmpty from "lodash.isempty";
export default {
  name: "TodoList",
  data() {
    return {
      newTodoItem: "",
      cachedItem: "",
      filter: "all",
      showById: null,
      selectBetween: {
        first: null,
        last: null
      },
      inBetween: false,
      list: [
        {
          id: 1,
          text: "Portakal Al",
          isDone: false,
          isEditing: false
        },
        {
          id: 2,
          text: "Elma Al",
          isDone: false,
          isEditing: false
        },
        {
          id: 3,
          text: "Armut Al",
          isDone: false,
          isEditing: false
        },
        {
          id: 4,
          text: "Salatalık Al",
          isDone: false,
          isEditing: false
        }
      ]
    };
  },
  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },
  computed: {
    showClearDone() {
      return this.list.filter(item => item.isDone).length > 0;
    },
    todoLeft() {
      return this.list.filter(item => !item.isDone).length;
    },
    allDone() {
      return this.todoLeft == 0;
    },
    isEmpty() {
      return isEmpty(this.list);
    }
  },
  methods: {
    addTodo() {
      if (this.newTodoItem.trim().length == 0) return;

      let lastId;
      if (!isEmpty(this.list)) {
        lastId = this.list[this.list.length - 1].id;
      } else {
        lastId = -1;
      }

      let todo = {
        id: ++lastId,
        text: this.newTodoItem,
        isDone: false,
        isEditing: false
      };

      this.list.push(todo);

      this.newTodoItem = "";
    },
    selectShift(id, event) {
      if (!event.target.checked) {
        if (id == this.selectBetween.first) {
          this.selectBetween.first = null;

          return;
        }
        if (id == this.selectBetween.last) {
          this.selectBetween.last = null;
        }
      }

      if (this.inBetween) {
        if (event.shiftKey && this.selectBetween.first != null) {
          this.selectBetween.last = id;

          let selectedList;

          if (this.selectBetween.first < this.selectBetween.last) {
            selectedList = this.list.slice(
              this.selectBetween.first,
              this.selectBetween.last + 1
            );

            this.list.map(item => (item.isDone = false));

            selectedList.map(item => (item.isDone = true));
          } else {
            selectedList = this.list.slice(
              this.selectBetween.last,
              this.selectBetween.first + 1
            );

            this.list.map(item => (item.isDone = false));
            selectedList.map(item => (item.isDone = true));
          }
        } else {
          this.selectBetween.first = id;
          this.inBetween = true;
        }
      } else {
        this.selectBetween.first = id;
        this.inBetween = true;
      }
    },
    editTodo(item) {
      if (item.text.trim().length == 0) item.text = this.cachedItem;

      item.isEditing = false;
    },
    removeItem(index) {
      this.list.splice(index, 1);
    },
    showEdit(item) {
      this.cachedItem = item.text;
      item.isEditing = true;
    },
    cancelEdit(item) {
      item.text = this.cachedItem;
      item.isEditing = false;
    },
    clearDone() {
      this.list = this.list.filter(item => !item.isDone);
    },
    checkAllList() {
      this.list.map(item => (item.isDone = true));
    },
    unCheckAllList() {
      this.list.map(item => (item.isDone = false));
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  lang="scss" scoped >
.is-done {
  position: relative;
  display: inline-block;
  &::after {
  background: rgba(0, 0, 0, 0.5);
  content: "";
  height: 0.125em;
  right: 0;
  left: 0;
  position: absolute;
  top: 50%;
}
}

.todo-container {
  width: 600px;
  margin: 50px auto;
  padding: 30px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
  position: relative;
}

.todo-form {
  display: flex;
  justify-content: space-between;
}

.todo-input {
  width: 100%;
  max-width: 400px;
  font-size: 18px;
  padding: 10px 10px 10px 10px;
  display: block;
  border: none;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}
.todo-button {
  width: 100%;
  background-color: #67daff;
  border-radius: 3px;
  font-weight: bold;
  border: none;
  max-width: 100px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

.todo-helpers-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.todo-clear-done-button {
  width: 100%;
  background-color: #c62828;
  border-radius: 3px;
  color: #fff;
  padding: 10px 0;
  font-weight: bold;
  border: none;
  max-width: 100px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

.todo-list {
  display: flex;
  flex-direction: column;
  width: 100%;
  padding: 0;
  margin: 20px 0;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

.todo-list-item {
  padding: 20px;
  background: #90a4ae5c;
  text-indent: 0;
  list-style-type: none;
  position: relative;
}

.todo-list-item:nth-child(even) {
  background: #37474f;
  color: #fff;
}

.todo-edit {
  padding: 5px;
  border-radius: 3px;
  outline: none;
  border: none;
}

.todo-cancel-button {
  background-color: #c62828;
  color: #fff;
  padding: 5px 3px;
  border-radius: 3px;
  border: none;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

.todo-done {
  display: inline-block;
}
.todo-text {
  margin-left: 10px;
  display: inline-block;
  cursor: pointer;

  font-size: 20px;
}

.todo-list-delete {
  cursor: pointer;
  position: absolute;
  right: 10px;
  bottom: calc(50% - 10px);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
