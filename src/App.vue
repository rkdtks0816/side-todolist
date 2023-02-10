<template>
  <!-- 제목 -->
  <header>
      <h1>To Do List</h1>
  </header>
  <!-- 입력 -->
  <div class="inputBox shadow">
      <input type="text" v-model="newTodoItem" placeholder="할 일을 입력해 주세요" v-on:keyup.enter="addTodo()">
      <span class="addContainer" v-on:click="addTodo()">
          <i class="far fa-plus addBtn" aria-hidden="true"></i>
      </span>
  </div>
  <!-- 출력 -->
  <section>
    <!-- <draggable :list="todoItems" class="list-group" ghost-class="ghost" @start="drag=true" @end="drag=false"> -->
      <TransitionGroup name="list" tag="ul">
        <li :class="[todoItem.checkstate === 0 ? 'todolist' : 'finishedlist']" v-for="(todoItem, index) in todoItems" v-bind:key="todoItem.Transitionkey">
          <span class="checkBtn" type="button" @click="checktodo(index)">
            <i class="far fa-check" aria-hidden="true"></i>
          </span>
          <span class="TodoItem" >{{ todoItem.TodoItem }}</span>
          <span class="dateStr" >{{ todoItem.dateStr }}</span>
          <span class="removeBtn" type="button" @click="removetodo(index)">
            <i class="far fa-trash-alt" aria-hidden="true"></i>
          </span>
        </li>
      </TransitionGroup>
    <!-- </draggable> -->
  </section>
  <!-- 전체삭제 -->
  <div class="clearAllContainer">
      <span class="clearAllBtn" v-on:click="clearTodo()">Clear All</span>
  </div>
</template>

<script>
// import draggable from "vuedraggable";

  export default {
    // components: {
    //   draggable
    // },
    data() {
        return {
          keynum: 0,
          newTodoItem: '',
          todoItems: [],
          drag: false
        }
    },
    mounted() {
        this.loadTodoList();
    },
    methods: {
      addTodo() {
        var newTodoItemJson = {};
        var checkstate = 0;

        if(this.newTodoItem == ''){
            alert('값을 입력해 주세요.');
            return;
        }
        const date = new Date();

        const year = date.getFullYear();
        const month = ('0' + (date.getMonth() + 1)).slice(-2);
        const day = ('0' + date.getDate()).slice(-2);
        const hours = ('0' + date.getHours()).slice(-2);
        const minutes = ('0' + date.getMinutes()).slice(-2);
        const seconds = ('0' + date.getSeconds()).slice(-2);
        const dateStr = year + '-' + month + '-' + day + ' ' + hours + ':' + minutes + ':' + seconds;

        var savetodolist = localStorage.getItem("todolist");
        var todolist = (savetodolist != null) ? JSON.parse(savetodolist) : []; 

        newTodoItemJson.Transitionkey = Number(year+month+day+hours+minutes+seconds) + this.keynum;
        newTodoItemJson.checkstate = checkstate;
        newTodoItemJson.TodoItem = this.newTodoItem;
        newTodoItemJson.dateStr = dateStr;

        todolist.push(newTodoItemJson);
        todolist.sort((a, b) => a['checkstate'] - b['checkstate'] )
        localStorage.setItem("todolist", JSON.stringify(todolist));
        this.newTodoItem="";
        this.loadTodoList();
        this.keynum++
      },
      loadTodoList() {
          var savetodolist = localStorage.getItem('todolist');
          this.todoItems = (savetodolist) ? JSON.parse(savetodolist) : [];
      },
      checktodo(index) {
        var savetodolist = localStorage.getItem("todolist");
        var todolist = (savetodolist != null) ? JSON.parse(savetodolist) : [];
        todolist[index].checkstate = (todolist[index].checkstate != 1) ? 1 : 0;
        todolist.sort((a, b) => a['checkstate'] - b['checkstate'] )
        localStorage.setItem("todolist", JSON.stringify(todolist));
        this.loadTodoList();
      },
      removetodo(index) {
        var savetodolist = localStorage.getItem("todolist");
        var todolist = (savetodolist != null) ? JSON.parse(savetodolist) : []; 
        todolist.splice(index, 1);
        todolist.sort((a, b) => a['checkstate'] - b['checkstate'] )
        localStorage.setItem("todolist", JSON.stringify(todolist));
        this.loadTodoList();
      },
      clearTodo() {
        localStorage.clear();
        this.loadTodoList();
      }
    }
  }

</script>

<style>
  body {
    text-align: center;
    background-color: #F6F6F6;
  }
  input {
    border-style: groove;
    width: 200px;
  }
  button {
    border-style: groove;
  }
  .shadow {
    box-shadow: 5px 10px 10px rgba(0, 0, 0, 0.03);
  }
  h1 {
    color: #2F3B52;
    font-weight: 900;
    margin: 2.5rem 0 1.5rem;
  }
  input:focus {
    outline: none;
  }
  .inputBox {
    width: 800px;
    background: white;
    height: 50px;
    line-height: 50px;
    border-radius: 5px;
    margin:0 auto; 
  }
  .inputBox input {
    float: left;
    width: calc(100% - 50px);
    height: 100%;
    border-style: none;
    font-size: 0.9rem;
    padding : 0;
    text-align: center;
  }
  .addContainer {
    float: right;
    background: linear-gradient(to right, #62EAC6, #32CEE6);
    display: block;
    width: 50px;
    border-radius: 0 5px 5px 0;
    cursor: pointer;
  }
  .addBtn {
    color: white;
    vertical-align: middle;
  }
  ul {
    width: 800px;
    list-style-type: none;
    padding-left: 0;
    margin-top: 0;
    text-align: left;
    margin:0 auto; 
  }
  .list-enter-active, .list-leave-active {
    transition: all 1s;
  }
  .list-enter-from, .list-leave-to {
    opacity: 0;
    transform: translateX(30px);
  }
  .todolist {
    display: flex;
    min-height: 50px;
    height: 50px;
    line-height: 50px;
    margin: 0.5rem 0;
    padding: 0 0.9rem;
    background: white;
    border-radius: 5px;
  }
  .todolist .checkBtn {
    color: #62acde;
    margin-right: 15px;
    cursor: pointer;
  }.TodoItem {
    margin-right: auto;
  }
  .dateStr {
    margin-left: auto;
  }
  .removeBtn {
    margin-left: 15px;
    color: #de4343;
    cursor: pointer;
  }
  .finishedlist {
    display: flex;
    min-height: 50px;
    height: 50px;
    line-height: 50px;
    margin: 0.5rem 0;
    padding: 0 0.9rem;
    background: white;
    border-radius: 5px;
    text-decoration: line-through;
    color: #575757;
  }
  .finishedlist .checkBtn {
    margin-right: auto;
    color: #575757;
    margin-right: 15px;
    cursor: pointer;
  }
  .clearAllContainer {
    width: 8.5rem;
    height: 50px;
    line-height: 50px;
    background-color: white;
    border-radius: 5px;
    margin: 15px auto;
    cursor: pointer;
  }
  .clearAllBtn {
    color: #e20303;
    display: block;
  } 
  .ghost {
    opacity: 0.5;
    background: #c8ebfb;
  }
  @media (max-width: 800px){
    .inputBox {
      width: 100%;
      background: white;
      height: 50px;
      line-height: 50px;
      border-radius: 5px;
    }
    ul {
      width: 100%;
      list-style-type: none;
      padding-left: 0;
      margin-top: 0;
      text-align: left;
    }
  }
</style>