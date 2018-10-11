<template>
  <div class="task__item">
    <h2 class="task__title text-md-left">{{data.taskName}}</h2>
    <div class="task__info row">
      <div class="task__project col-md">
        Проект: <b>{{data.projectName}}</b>
      </div>
      <div class="task__priority col-md text-md-right">
        Приоритет: <b>{{data.priority}}</b>
      </div>
    </div>
    <div class="task__description" v-if="showDescription">
      {{data.description}}
    </div>
    <div class="app__form">
      <div class="form-group row text-center">
        <div class="col-md text-md-left">
          <button @click.prevent="editTask" type="button" class="btn btn-outline-primary">Изменить</button>
        </div>
        <div class="col-md text-md-center">
          <button @click.prevent="closeTask" type="button" class="btn btn-outline-danger">Закрыть</button>
        </div>
        
        <div class="col-md text-md-right">
          <button v-if="showDescription" @click.prevent="showDescription = !showDescription" type="button" class="btn btn-primary">Свернуть</button>
          <button v-else type="button" @click.prevent="showDescription = !showDescription" class="btn btn-outline-primary">Развернуть</button>
        </div> 

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Task',
  props: {
    data: {
      type: Object
    }
  },
  components: {
    
  },
  data() {
    return {
      showDescription: false
    }
  },
  created() {

  },
  methods: {
    closeTask() {
      this.$emit('onCloseTask', this.data.id)
    },
    editTask() {
      this.$emit('onEditTask', this.data.id)
    }
  }
}
</script>

<style scoped lang="scss">

  @import '@/assets/styles/_variables.scss';

  .task {

    &__item {
      display: block;
      margin: 20px 10px 0 0;
      padding: 20px;
      border: 1px solid rgba(0,0,0,0.19);

      &:first-child {
        margin-top: 0;
      }
    }

    &__title {
      font-size: 22px;
      line-height: 24px;
      color: $color-text;
      margin: 0 0 20px 0;
      padding: 0;
      text-align: center;
    }

    &__info {
      font-size: 16px;
      line-height: 18px;
      color: $color-text;
      padding: 0;
    }

    &__project {
      font-weight: 500;
      margin: 0 0 20px 0;   
    }

    &__priority {
      font-weight: 500;
      margin: 0 0 20px 0;   
    }

    &__description {
      font-size: 14px;
      line-height: 18px;
      margin: 0 0 20px 0;
      text-align: justify;
    }

  }

</style>