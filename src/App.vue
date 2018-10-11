<template>
  <div class="container-fluid d-md-flex h-100 p-0">

    <transition name="fade" mode="out-in" appear>

      <div v-if="isLoaded" key="view" class="col-md-8 col-lg-6 col-xl-5 ml-auto mr-auto">

        <h1 class="app__title">Todo:</h1>

        <!-- Task List -->
        <div class="app__box">
          <div v-if="tasksFiltered.length" class="app__task-list">        
            <Task v-for="task in tasksFiltered" 
              :data="task" 
              :key="task.id" 
              @onCloseTask="closeTask" 
              @onEditTask="editTask" 
            />
          </div>
          <div v-else class="app__task-empty">
            Список задач пуст!!!<br>Хоошая работа, время отдохнуть ;)
          </div>
        </div>

        <!-- Control Panel -->
        <transition name="fade" mode="out-in" appear>
        <Control v-if="showPanel"
          class="app__box"           
          @onCreateTask="createTask" 
          @onOrderPriority="orderPriority" 
          :priority="priority"
          :projects = projects
          :projectDefault = projectDefault
        />
        </transition>

        <!-- New/Edit Form -->
        <transition name="fade" mode="out-in" appear>
        <Form v-if="showForm" 
          class="app__box"
          @onSaveTask="saveTask" 
          @onCancelForm="cancelForm" 
          :task="editedTask" 
        />
        </transition>

      </div>
      <div v-else key="loading" class="col-md-8 col-lg-6 col-xl-5 m-auto">
        <div class="app__title">Загрузка ...</div>
      </div>

    </transition>

  </div>
</template>

<script>
import Task from './components/Task.vue'
import Control from './components/Control.vue'
import Form from './components/Form.vue'

const STORAGE_KEY = "todo-tasks"

export default {
  name: 'app',
  components: { Task, Control, Form },
  data() {
    return {
      isLoaded: false,
      tasks: [],
      editedTask: {
        priority: 1
      },
      priority: false,
      projectDefault: "all",
      projects: {
        selected: "",
        list: []
      },
      showPanel: true,
      showForm: false,      
    }
  },
  created() {
    this.loadData()
  },
  computed: {
    tasksFiltered() {
      //console.log("tasksFiltered")

      // project sort
      //console.log("selected", this.projects.selected)
      let filtered = this.tasks

      if ( this.projects.selected !== this.projectDefault ) {
        filtered = this.tasks.filter((task) => task.projectName === this.projects.selected )
      }

      // priority sort
      //console.log("priority", this.priority)      
      let sorted = [...filtered]

      if ( this.priority ) {
        sorted.sort(this.compareValues("priority"))
      }

      return sorted
    }
  },
  methods: {
    loadData() {        
      this.tasks = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
      this.updateProjects()
      this.projects.selected = this.projectDefault

      this.isLoaded = true        
    },
    createTask() {
      this.editedTask = { priority: 1}
      this.showPanel = false
      this.showForm = true
    },
    cancelForm() {
      this.editedTask = { priority: 1}
      this.showPanel = true
      this.showForm = false
    },
    saveTask(task) {
      //console.log('App: task', task.taskName)

      // is New Task?
      if ( task.id ) {
        //console.log("Edited Task")
        let index = this.tasks.map(filteredTask => filteredTask.id).indexOf(task.id)
        this.tasks.splice(index, 1, task)
      } else {
        //console.log("New Task")
        task.id = new Date().getTime()
        this.tasks.push(task)
      }

      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks))

      this.updateProjects()

      // if selected projectName !== task projectName => show all Tasks
      if ( this.projects.selected !== task.projectName ) {
        this.projects.selected = this.projectDefault
      }
    
      this.editedTask = { priority: 1}
      this.showPanel = true
      this.showForm = false
    },
    editTask(id) {
      let task = this.tasks.filter((task) => task.id === id)[0]
      this.editedTask = {...task}

      this.showPanel = false
      this.showForm = true
    },
    closeTask(id) {
      //console.log("App, closeTask = id", id)
      let filteredTasks = this.tasks.filter((task) => task.id !== id)

      this.tasks = filteredTasks
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks))

      this.updateProjects()

      this.editedTask = { priority: 1}
      this.showPanel = true
      this.showForm = false
    },
    orderPriority(val) {
      this.priority = val
    },
    updateProjects() {
      //console.log("updateProjects()")
      let projects = this.tasks.map(function(task){
        return task.projectName
      })

      // remove duplicate
      projects = projects.filter((v, i, a) => a.indexOf(v) === i)
      this.projects.list = projects

      if ( !this.projects.list.includes(""+this.projects.selected) ) {
        this.projects.selected = this.projectDefault
      }

    },
    compareValues(key, order='asc') {
      return function(a, b) {
        if(!a.hasOwnProperty(key) || !b.hasOwnProperty(key)) {
            return 0; 
        }

        const varA = (typeof a[key] === 'string') ? 
          a[key].toUpperCase() : a[key];
        const varB = (typeof b[key] === 'string') ? 
          b[key].toUpperCase() : b[key];

        let comparison = 0;
        if (varA > varB) {
          comparison = 1;
        } else if (varA < varB) {
          comparison = -1;
        }
        return (
          (order == 'desc') ? (comparison * -1) : comparison
        );
      };
    }
  } 
}
</script>

<style lang="scss">

  @import './assets/styles/_variables.scss';
  @import './assets/styles/bundle.scss';

  .fade-enter-active {
    transition: opacity .3s ease;
  }
  .fade-leave-active {
    transition: opacity 0 ease;
    width: 100%;    
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
    position: absolute;
  } 

  .app {

    &__title {
      text-align: center;
      font-size: 32px;
      line-height: 36px;
      color: #fff;
      margin: 20px 0;
      padding: 0;
    }

    &__box {
      padding: 20px;
      margin: 20px auto;
      border-radius: 4px; 
      background-color: #FFFFFF;  
      box-shadow: 0 40px 77px 0 rgba(0,0,0,0.19), 
          0 27px 44px 0 rgba(0,0,0,0.24);
    }

    &__task-list {
      overflow: hidden;
      overflow-y: auto;
      max-height: 526px;
      margin: 0 -10px 0 0;
    }

    &__task-empty {
      text-align: center;
      display: block;
      margin: 0;
      padding: 20px;
      border: 1px solid rgba(0,0,0,0.19);
    }

    &__form {
      width: 100%;
      padding: 0px;
    }

  }

  @include media-breakpoint-down(sm) {
    .app {

      &__title {
        color: $color-text;
      }

      &__task-list {
        max-height: 336px;
      }

      &__box {
        border: 1px solid rgba(0,0,0,0.19);
        box-shadow: none;
      }

    }
  }

</style>