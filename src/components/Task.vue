<template>
  <div>
    <Header/>
    <!-- Category filter button -->
    <main class="main">
      <div class="showchange">
        <ul class="tab">
          <li><button v-on:click="activetab=0" v-bind:class="[ activetab === 0 ? 'tab-box__active' : '' ]" class="tab-box">All</button></li>
          <li><button v-on:click="activetab=3" v-bind:class="[ activetab === 3 ? 'tab-box__active' : '' ]" class="tab-box">Bucket List</button></li>
          <li><button v-on:click="activetab=2" v-bind:class="[ activetab === 2 ? 'tab-box__active' : '' ]" class="tab-box">Private</button></li>
          <li><button v-on:click="activetab=1" v-bind:class="[ activetab === 1 ? 'tab-box__active' : '' ]" class="tab-box">Recruit</button></li>
        </ul>

        <ul class="filter">
          <li class="filter-item"><button class="filter-btn" type="submit" v-on:click="showTodoType = 'all'">All</button></li>
          <li class="filter-item"><button class="filter-btn" type="submit" v-on:click="showTodoType = 'active'">Active</button></li>
          <li class="filter-item"><button class="filter-btn" type="submit" v-on:click="showTodoType = 'complete'">Complete</button></li>
        </ul>
      </div>

      <div class="container">
        <div v-show="activetab === 0" class="category">
          <!-- listの一覧表示 -->
          <div v-for="(taskCategory, key) in filteredTodos" :key="taskCategory.id" class="category-box">
            <div class="card">
              <p class="card-category-name">{{ key }}</p>
              <div v-for="(list, subkey) in taskCategory" :key="list.id" class="card-list">

                <!-- list名＋編集btn -->
                <div class="card-namebox">
                  <p class="card-name">{{list.name}}</p>
                  <button type="button" @click="list.fixListShow=!list.fixListShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                  <button type="submit" v-on:click="deleteLists(list, subkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                </div>

                <!-- list再編集 -->
                <div v-show="list.fixListShow" class="popup">
                  <div  class="popup-fix__todo">
                    <p class="popup-item">List name</p>
                    <input type="text" v-model="list.name" class=" popup-input">

                    <p class="popup-item">Category</p>
                    <select v-model="selectCategory" class="popup-input">
                      <option v-for="option in options" v-bind:value="option.value" :key="option.id">
                        {{ option.text }}
                      </option>
                    </select>

                    <div class="popup-btn-box">
                      <button type="button" @click="list.fixListShow=!list.fixListShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                      <button type="submit" v-on:click="fixList(list, subkey)" class="popup-btn popup-btn__positive">Done</button>
                    </div>
                  </div>
                </div>

                <!-- todoを表示 -->
                <ul>
                  <li v-for="(todo, subsubkey) in list.subTasks" :key="todo.id">
                    <div v-if="todo.todoStatus == true" class="card-item">
                      <input type="checkbox" v-model="todo.isComplete">
                      <label v-on:click="updateIsCompleteTodo(subkey, todo, list, subsubkey)" class="card-item-name">
                        <span>{{todo.subName}}</span>
                        <span>{{changeShowDate(todo)}}</span>
                      </label>

                      <!-- Todo編集btnと削除btn -->
                      <div>
                        <!-- todo編集btn -->
                        <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                        <button type="submit" v-on:click="deleteTodos(subkey, list, subsubkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                      </div>
                    </div>

                    <!-- todo再編集 -->
                    <div v-show="todo.fixTodoShow" class="popup">
                      <div class="popup-fix__todo">
                        <p class="popup-item">Todo name</p>
                        <input type="text" v-model="todo.subName" v-bind:text="todo.subName" class="popup-input">

                        <p class="popup-item">Dead line</p>
                        <input type="date" v-model="deadline" placeholder="2020-01-01" class="popup-input">

                        <div class="popup-btn-box">
                          <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                          <button type="submit" v-on:click="addDeadlineFixName(list, todo, subkey, subsubkey); todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__positive">Done</button>
                        </div>
                      </div>
                    </div>

                  </li>
                </ul>

                <div>
                  <input type="text" v-model="newTodoName" placeholder="Make Todo's name" class="popup-input">
                  <button type="button" v-on:click="createTodo(list, subkey)" class="btn btn-done"><span class="fa fa-check"></span></button>
                </div>
              </div>
              <button  v-if="windowW >= 1024" type="button" name="makelist" @click="show=!show" class="btn-makelist" v-bind:class="{actives: show}">
                <span>リスト追加</span>
              </button>
            </div>
          </div>
        </div>
        <div v-show="activetab === 3" class="tabcontent">
          <!-- listの一覧表示 -->
          <div v-for="(taskCategory, key) in filteredTodos" :key="taskCategory.id" >
            <div v-if="key == 'bucket list'" class="card">
              <p class="card-category-name card-category-name__scrollH">{{ key }}</p>
              <div class="card-scrollH">
                <div v-for="(list, subkey) in taskCategory" :key="list.id" class="card-list card-list__scrollH">
                  <!-- list名＋編集btn -->
                  <div class="card-namebox">
                    <p class="card-name">{{list.name}}</p>
                    <button type="button" @click="list.fixListShow=!list.fixListShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                    <button type="submit" v-on:click="deleteLists(list, subkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                  </div>

                  <!-- list再編集 -->
                  <div v-show="list.fixListShow" class="popup">
                    <div  class="popup-fix__todo">
                      <p class="popup-item">List name</p>
                      <input type="text" v-model="list.name" class=" popup-input">

                      <p class="popup-item">Category</p>
                      <select v-model="selectCategory" class="popup-input">
                        <option v-for="option in options" v-bind:value="option.value" :key="option.id">
                          {{ option.text }}
                        </option>
                      </select>

                      <div class="popup-btn-box">
                        <button type="button" @click="list.fixListShow=!list.fixListShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                        <button type="submit" v-on:click="fixList(list, subkey)" class="popup-btn popup-btn__positive">Done</button>
                      </div>
                    </div>
                  </div>

                  <!-- todoを表示 -->
                  <ul>
                    <li v-for="(todo, subsubkey) in list.subTasks" :key="todo.id">
                      <div v-if="todo.todoStatus == true" class="card-item">
                        <input type="checkbox" v-model="todo.isComplete">
                        <label v-on:click="updateIsCompleteTodo(subkey, todo, list, subsubkey)" class="card-item-name">
                          <span>{{todo.subName}}</span>
                          <span>{{changeShowDate(todo)}}</span>
                        </label>

                        <!-- Todo編集btnと削除btn -->
                        <div>
                          <!-- todo編集btn -->
                          <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                          <button type="submit" v-on:click="deleteTodos(subkey, list, subsubkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                        </div>
                      </div>

                      <!-- todo再編集 -->
                      <div v-show="todo.fixTodoShow" class="popup">
                        <div class="popup-fix__todo">
                          <p class="popup-item">Todo name</p>
                          <input type="text" v-model="todo.subName" v-bind:text="todo.subName" class="popup-input">

                          <p class="popup-item">Dead line</p>
                          <input type="date" v-model="deadline" placeholder="2020-01-01" class="popup-input">

                          <div class="popup-btn-box">
                            <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                            <button type="submit" v-on:click="addDeadlineFixName(list, todo, subkey, subsubkey); todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__positive">Done</button>
                          </div>
                        </div>
                      </div>

                    </li>
                  </ul>

                  <div>
                    <input type="text" v-model="newTodoName" placeholder="Make Todo's name" class="popup-input">
                    <button type="button" v-on:click="createTodo(list, subkey)" class="btn btn-done"><span class="fa fa-check"></span></button>
                  </div>
                </div>
                <button v-if="windowW >= 1024" type="button" name="makelist" @click="show=!show" class="btn-makelist btn-makelist__scrollH" v-bind:class="{actives: show}">
                  <span>リスト追加</span>
                </button>
              </div>
            </div>
          </div>
        </div>
        <div v-show="activetab === 2" class="tabcontent">
          <!-- listの一覧表示 -->
          <div v-for="(taskCategory, key) in filteredTodos" :key="taskCategory.id" >
            <div v-if="key == 'private'" class="card">
              <p class="card-category-name card-category-name__scrollH">{{ key }}</p>
              <div class="card-scrollH">
                <div v-for="(list, subkey) in taskCategory" :key="list.id" class="card-list card-list__scrollH">
                  <!-- list名＋編集btn -->
                  <div class="card-namebox">
                    <p class="card-name">{{list.name}}</p>
                    <button type="button" @click="list.fixListShow=!list.fixListShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                    <button type="submit" v-on:click="deleteLists(list, subkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                  </div>

                  <!-- list再編集 -->
                  <div v-show="list.fixListShow" class="popup">
                    <div  class="popup-fix__todo">
                      <p class="popup-item">List name</p>
                      <input type="text" v-model="list.name" class=" popup-input">

                      <p class="popup-item">Category</p>
                      <select v-model="selectCategory" class="popup-input">
                        <option v-for="option in options" v-bind:value="option.value" :key="option.id">
                          {{ option.text }}
                        </option>
                      </select>

                      <div class="popup-btn-box">
                        <button type="button" @click="list.fixListShow=!list.fixListShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                        <button type="submit" v-on:click="fixList(list, subkey)" class="popup-btn popup-btn__positive">Done</button>
                      </div>
                    </div>
                  </div>

                  <!-- todoを表示 -->
                  <ul>
                    <li v-for="(todo, subsubkey) in list.subTasks" :key="todo.id">
                      <div v-if="todo.todoStatus == true" class="card-item">
                        <input type="checkbox" v-model="todo.isComplete">
                        <label v-on:click="updateIsCompleteTodo(subkey, todo, list, subsubkey)" class="card-item-name">
                          <span>{{todo.subName}}</span>
                          <span>{{changeShowDate(todo)}}</span>
                        </label>

                        <!-- Todo編集btnと削除btn -->
                        <div>
                          <!-- todo編集btn -->
                          <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                          <button type="submit" v-on:click="deleteTodos(subkey, list, subsubkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                        </div>
                      </div>

                      <!-- todo再編集 -->
                      <div v-show="todo.fixTodoShow" class="popup">
                        <div class="popup-fix__todo">
                          <p class="popup-item">Todo name</p>
                          <input type="text" v-model="todo.subName" v-bind:text="todo.subName" class="popup-input">

                          <p class="popup-item">Dead line</p>
                          <input type="date" v-model="deadline" placeholder="2020-01-01" class="popup-input">

                          <div class="popup-btn-box">
                            <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                            <button type="submit" v-on:click="addDeadlineFixName(list, todo, subkey, subsubkey); todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__positive">Done</button>
                          </div>
                        </div>
                      </div>

                    </li>
                  </ul>

                  <div>
                    <input type="text" v-model="newTodoName" placeholder="Make Todo's name" class="popup-input">
                    <button type="button" v-on:click="createTodo(list, subkey)" class="btn btn-done"><span class="fa fa-check"></span></button>
                  </div>
                </div>
                <button v-if="windowW >= 1024" type="button" name="makelist" @click="show=!show" class="btn-makelist btn-makelist__scrollH" v-bind:class="{actives: show}">
                  <span>リスト追加</span>
                </button>
              </div>
            </div>
          </div>
        </div>
        <div v-show="activetab === 1" class="tabcontent">
          <!-- listの一覧表示 -->
          <div v-for="(taskCategory, key) in filteredTodos" :key="taskCategory.id" >
            <div v-if="key == 'recruit'" class="card">
              <p class="card-category-name card-category-name__scrollH">{{ key }}</p>
              <div class="card-scrollH">
                <div v-for="(list, subkey) in taskCategory" :key="list.id" class="card-list card-list__scrollH">
                  <!-- list名＋編集btn -->
                  <div class="card-namebox">
                    <p class="card-name">{{list.name}}</p>
                    <button type="button" @click="list.fixListShow=!list.fixListShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                    <button type="submit" v-on:click="deleteLists(list, subkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                  </div>

                  <!-- list再編集 -->
                  <div v-show="list.fixListShow" class="popup">
                    <div  class="popup-fix__todo">
                      <p class="popup-item">List name</p>
                      <input type="text" v-model="list.name" class=" popup-input">

                      <p class="popup-item">Category</p>
                      <select v-model="selectCategory" class="popup-input">
                        <option v-for="option in options" v-bind:value="option.value" :key="option.id">
                          {{ option.text }}
                        </option>
                      </select>

                      <div class="popup-btn-box">
                        <button type="button" @click="list.fixListShow=!list.fixListShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                        <button type="submit" v-on:click="fixList(list, subkey)" class="popup-btn popup-btn__positive">Done</button>
                      </div>
                    </div>
                  </div>

                  <!-- todoを表示 -->
                  <ul>
                    <li v-for="(todo, subsubkey) in list.subTasks" :key="todo.id">
                      <div v-if="todo.todoStatus == true" class="card-item">
                        <input type="checkbox" v-model="todo.isComplete">
                        <label v-on:click="updateIsCompleteTodo(subkey, todo, list, subsubkey)" class="card-item-name">
                          <span>{{todo.subName}}</span>
                          <span>{{changeShowDate(todo)}}</span>
                        </label>

                        <!-- Todo編集btnと削除btn -->
                        <div>
                          <!-- todo編集btn -->
                          <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="btn btn-fix"><span class="fa fa-pencil btn-fix"></span></button>
                          <button type="submit" v-on:click="deleteTodos(subkey, list, subsubkey)" class="btn btn-delete"><span class="fa fa-trash"></span></button>
                        </div>
                      </div>

                      <!-- todo再編集 -->
                      <div v-show="todo.fixTodoShow" class="popup">
                        <div class="popup-fix__todo">
                          <p class="popup-item">Todo name</p>
                          <input type="text" v-model="todo.subName" v-bind:text="todo.subName" class="popup-input">

                          <p class="popup-item">Dead line</p>
                          <input type="date" v-model="deadline" placeholder="2020-01-01" class="popup-input">

                          <div class="popup-btn-box">
                            <button type="button" @click="todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
                            <button type="submit" v-on:click="addDeadlineFixName(list, todo, subkey, subsubkey); todo.fixTodoShow=!todo.fixTodoShow" class="popup-btn popup-btn__positive">Done</button>
                          </div>
                        </div>
                      </div>
                    </li>
                  </ul>

                  <div>
                    <input type="text" v-model="newTodoName" placeholder="Make Todo's name" class="popup-input">
                    <button type="button" v-on:click="createTodo(list, subkey)" class="btn btn-done"><span class="fa fa-check"></span></button>
                  </div>
                </div>
                <button v-if="windowW >= 1024" type="button" name="makelist" @click="show=!show" class="btn-makelist btn-makelist__scrollH" v-bind:class="{actives: show}">
                  <span>リスト追加</span>
                </button>
              </div>
            </div>
          </div>
        </div>

      </div>

      <!-- Make list button -->
      <button type="button" name="makelist" @click="show=!show" class="btn-round btn-round__left" v-bind:class="{actives: show}" v-if="windowW < 1024">
        <span v-if="show == false" class="fa fa-list"></span>
        <span v-else class="fa fa-times"></span>
      </button>

      <!-- Make list contents -->
      <div v-show="show" class="popup">
        <div class="popup-fix__todo">
          <p class="popup-item">List name</p>
          <input type="text" v-model="newlistName" class="popup-input">

          <p class="popup-item">Category</p>
          <select v-model="selectCategory" class="popup-input">
            <option v-for="option in options" v-bind:value="option.value" :key="option.id">
              {{ option.text }}
            </option>
          </select>

          <div class="popup-btn-box">
            <button type="button" @click="show=!show" class="popup-btn popup-btn__negative popup-btn__left">Cancel</button>
            <button type="submit" @click="createList()" class="popup-btn popup-btn__positive">Done</button>
          </div>
        </div>
      </div>

      <router-link to="/calendar" v-if="windowW < 1024">
        <button type="button" name="makelist" class="btn-round btn-round__right">
          <span class="fa fa-calendar-check-o"></span>
        </button>
      </router-link>
    </main>

  </div>
</template>

<script>
import Header from '@/components/Header.vue';
import firebase from "firebase";

export default {
  name: "Task",
  data() {
    return {
      windowW: window.innerWidth,
      windowH: window.innerHeight,
      show: false,
      activetab: 0,
      database: null,
      todosRef: null,
      newlistName: '',
      fixListName: '',
      newTodoName: '',
      fixTodoName: '',
      deadline: '',
      showTodoType: "all",
      selectCategory: '',
      options: [
        { text: 'bucket list', value: 'bucket list' },
        { text: 'private', value: 'private' },
        { text: 'recruit', value: 'recruit' },
      ],
      todos: []
    };
  },
  components: {
    Header
  },
  created: function() {
    this.db = firebase.database();
    this.uid = firebase.auth().currentUser.uid;
    // refはreferenceでデータベースにある特定の項目を指し示すメソッド
    this.todosRef = this.db.ref("todos/" + this.uid);
    this.test = this.db.ref("todos/" + this.uid + "/")
    // データに変更があると実行されるfunction
    var _this = this;
    this.todosRef.on('value', function(snapshot) {
      _this.todos = snapshot.val(); // データに変化が起きたときに再取得する
    });
  },
  computed: {
    filteredTodos: function () {
      var showComplete = false;
      if (this.showTodoType == 'complete') {
        showComplete = true;
      }
      for (let key in this.todos) {
        let taskCategory = this.todos[key];
        // console.log(taskCategory);
        for (let subkey in taskCategory) {
          let list = taskCategory[subkey];
          // console.log(list);
          for (let subsubkey in list.subTasks) {
            let todo = list.subTasks[subsubkey];
             if (this.showTodoType == 'all') {
               todo.todoStatus = true;
             }
              else if(todo.isComplete == showComplete) {
                todo.todoStatus = true;
              }else {
                todo.todoStatus = false;
              }
          }
        }
      }
      return this.todos;
    },
    taskNumber: function () {
      let showComplete = false,
          allCount = 0,
          count = 0;
      if (this.showTodoType == 'complete') {
        showComplete = true;
      }
      for (let key in this.todos) {
        let taskCategory = this.todos[key];
        for (let subkey in taskCategory) {
          let list = taskCategory[subkey];
          for (let subsubkey in list.subTasks) {
            let todo = list.subTasks[subsubkey];
            console.log(todo);
            allCount += 1;
            if (this.showTodoType == 'all');
            else if (todo.isComplete == showComplete) {
              count += 1;
            }
          }
        }
      }
      if (this.showTodoType == 'all') {
        return allCount;
      } else {
        return count;
      }
    }
  },
  methods: {
    handleResize: function() {
      // resizeのたびにこいつが発火するので、ここでやりたいことをやる
      this.windowW = window.innerWidth;
      this.windowH = window.innerHeight;
    },
    createList: function() {
      if (this.newlistName == "") { return; }
      if (this.selectCategory == ""){return; }
      else  {
        this.todosRef.child(this.selectCategory).push({
          fixListShow: false,
          havechildren: 0,
          name: this.newlistName,
          category: this.selectCategory,
          achievementRate: 0,
        })
      }
      this.show = false;
      this.newlistName = "";
    },
    fixList: function (list, subkey) {
      if (list.name == "") { return; }
      if (this.selectCategory == ""){return; }
      else  {
        console.log("修正完了！");
        let fixListName = list.name
        list.name = fixListName;
        list.category = this.selectCategory;
        let fixed = {};
        fixed[subkey] = list;
        list.fixListShow = false
        this.todosRef.child(list.category).update(fixed)
      }
      this.fixListName = "";
      this.selectCategory = "";
    },
    createTodo: function(list, subkey) {
      console.log(list.category);
      if (this.newTodoName == "") { return; }
      else {
        this.todosRef.child(list.category).child(subkey).child("/subTasks").push({
          subName: this.newTodoName,
          isComplete: false,
          subDate: this.deadline,
          todoStatus: false,
          fixTodoShow: false,
        })
      }
      this.newTodoName = "";
      this.deadline = "";
    },
    addDeadlineFixName: function (list, todo, subkey, subsubkey) {
      if (todo.subName == "") { return; }
      else  {
        let newname = todo.subName
        console.log("修正完了！");
        todo.subName = newname;
        todo.subDate = this.deadline;
        todo.fixTodoShow = false;

        let addDateFixName = {};
        addDateFixName[subsubkey] = todo;

        this.todosRef.child(list.category).child(subkey).child("/subTasks").update(addDateFixName)
      }
      this.fixListName = "";
      this.deadline = "";
    },
    updateIsCompleteTodo: function(subkey, todo, list, subsubkey) {
      todo.isComplete = !todo.isComplete;
      var updates = {};
      updates[subsubkey] = todo;
      console.log(list);
      this.todosRef.child(list.category).child(subkey).child("/subTasks").update(updates)
    },
    changeShowDate: function(todo) {
      let planeDate = todo.subDate;
      let changeDate = planeDate.slice(-5);

      return changeDate;
    },
    deleteLists: function(list, subkey) {
      this.todosRef.child(list.category).child(subkey).remove();
    },
    // todoの削除
    deleteTodos: function(subkey, list, subsubkey) {
      this.todosRef.child(list.category).child(subkey).child("/subTasks").child(subsubkey).remove();
    }
  },
  mounted: function () {
    window.addEventListener('resize', this.handleResize)
  },
  beforeDestroy: function () {
    window.removeEventListener('resize', this.handleResize)
  }
};
</script>

<style scoped>
@media screen and (min-width:1024px) {
  .filter {
    width: calc(100vw - 384px);
    justify-content: flex-start;
    left: 384px;
    transform: none;
  }
  .card {

  }
    .card-category-name__scrollH {
      margin-left: 16px;
    }
}
</style>
