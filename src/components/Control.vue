<template>
  <div>
    <div class="app__form">
      <div class="form-group row text-center">
        <div class="col-md text-md-left">
          <button @click.prevent="createTask" type="button" class="btn btn-primary">Новая задача</button>
        </div>
        <div class="col-md">
          <div class="custom-control custom-checkbox">
            <input type="checkbox" class="custom-control-input" id="priority" 
              v-model="proxyPriority"
              @change="orderPriority"
            >
            <label class="custom-control-label small text-nowrap" for="priority">по приоритету</label>
          </div>
        </div>
        <div class="col-md text-md-right">
          <select class="form-control"
            v-model="projects.selected"
            :required="true"
          >
            <option :value="projectDefault" :selected="true">Все</option>
            <option v-for="project in projects.list" :value="project" :key="project">{{project}}</option>
          </select>
        </div>              
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Control',
  props: {
    priority: {
      type: Boolean
    },
    projects: {
      selected: String,
      list: Array
    },
    projectDefault: String
  },
  data() {
    return {
      proxyPriority: {
        type: Boolean
      }    
    }
  },
  created() {
    this.proxyPriority = this.priority
  },
  methods: {
    createTask() {
      this.$emit('onCreateTask')
    },
    orderPriority() {
      this.$emit('onOrderPriority', this.proxyPriority)      
    }
  },
}
</script>

<style scoped lang="scss">

</style>