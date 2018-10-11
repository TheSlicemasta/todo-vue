<template>
  <form @submit.prevent="saveTask">
    <div class="app__form">
      <div class="form-group row">
        <label for="taskName" class="col-md-4 col-form-label text-left text-md-right">Название задачи</label>
        <div class="col-md-8">
          <input type="text" class="form-control" id="taskName" placeholder="Название задачи" @focus="onFocus" v-model="task.taskName" ref="taskName">
        </div>
      </div>
      <div class="form-group row">
        <label for="projectName" class="col-md-4 col-form-label text-left text-md-right">Название проекта</label>
        <div class="col-md-8">
          <input type="text" class="form-control" id="projectName" placeholder="Название проекта" @focus="onFocus" v-model="task.projectName" ref="projectName">
        </div>
      </div>
      <div class="form-group row">
        <label for="priority" class="col-md-4 col-form-label text-left text-md-right">Приоритет</label>
        <div class="col-md-8">
          <select class="form-control" id="priority" v-model="task.priority" ref="priority">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
          </select>               
        </div>
      </div>
      <div class="form-group row">
        <label for="description" class="col-md-4 col-form-label text-left text-md-right">Описание</label>
        <div class="col-md-8">
          <textarea class="form-control" id="description" placeholder="Описание" cols="" rows="" @focus="onFocus" v-model="task.description" ref="description"></textarea>
        </div>
      </div>
      <div class="form-group row">
        <div class="col-md-8 offset-md-4">
          <div class="row text-center">
            <div class="col text-md-left">
              <button type="submit" class="btn btn-outline-primary">Сохранить</button>
            </div>
            <div class="col text-md-right">
              <button @click.prevent="cancelForm" type="button" class="btn btn-outline-danger">Отмена</button>
            </div>
          </div>              
        </div>
      </div>
    </div>
  </form>
</template>

<script>
export default {
  name: 'Form',
  props: {
    task: {
      id: Number,
      taskName: String,
      projectName: String,
      priority: String,
      description: String
    }
  },
  data() {
    return {
      isLoaded: false
    }
  },
  created() {
    
  },
  methods: {
    saveTask() {
      //console.log("Form: this.task ", this.task)
      let hasError = false

      if ( !this.task.taskName ) {
        hasError = true
        this.$refs.taskName.classList.add("is-invalid")
      } else {
        this.$refs.taskName.classList.remove("is-invalid")
      }

      if ( !this.task.projectName ) {
        hasError = true
        this.$refs.projectName.classList.add("is-invalid")
      } else {
        this.$refs.projectName.classList.remove("is-invalid")
      }

      if ( !this.task.description ) {
        hasError = true
        this.$refs.description.classList.add("is-invalid")
      } else {
        this.$refs.description.classList.remove("is-invalid")
      }

      if (!hasError) {
        this.$emit('onSaveTask', this.task)

        this.$refs.taskName.value = ""
        this.$refs.projectName.value = ""
        this.$refs.description.value = ""
      }
    },
    cancelForm() {
      this.$emit('onCancelForm')
    },
    onFocus(event) {
      event.target.classList.remove("is-invalid")
    }
  }
}
</script>

<style scoped lang="scss">

</style>