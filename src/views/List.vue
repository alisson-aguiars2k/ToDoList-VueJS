<template>
  <div class="container mt-2">
    <template v-if="isTasksEmpty">
      <div class="empty">
        <div class="empty-data">
          <img src="../assets/images/undraw_add_notes_re_ln36.svg" class="empty-data-image">
          <b-button
          variant="outline-primary"
          class="mt-2"
          size="lg"
          to="/form"
          >Criar tarefa</b-button>
        </div>
      </div>
    </template>

    <template v-else>
        <div v-for="(task, index) in tasks" :key="index">
              <b-card :title="task.subject" class="mb-2">
                <b-card-text>{{ task.description }}</b-card-text>

                <b-button variant="outline-secondary" class="mr-2" @click="edit(index)">Editar</b-button>
                <b-button variant="outline-danger" class="mr-2" @click="remove(task, index)">Excluir</b-button>
              </b-card>
        </div>
    </template>

    
    <!-- Modal para excluir tarefas -->
    <b-modal ref="modalRemove" hide-footer title="Exclusão de tarefa">
      <div class="d-block text-center">
        Deseja excluir essa terefa ? <br> {{ taskSelected.subject }}
      </div>
      <div class="mt-3 d-flex justify-content-end">
        <b-button variant="outline-secondary" class="mr-2" @click="hideModal">Cancelar</b-button>
        <b-button variant="outline-danger" class="mr-2" @click="confirmRemoveTask">Excluir</b-button>
      </div>
    </b-modal>
    <!-- Fim do modal -->
  </div>
</template>

<script>

  export default {
    name:'List',

    data() {
      return {
        tasks: [],
        taskSelected: []
      }
    },
    created(){
      this.tasks = (localStorage.getItem("tasks")) ? JSON.parse(localStorage.getItem("tasks")) : [];
    },
    methods: {
      edit(index) {
        this.$router.push({ name: "form", params: {index} })
      },
      remove(task, index) {
        this.taskSelected = task;
        this.taskSelected.index = index;
        this.$refs.modalRemove.show();
      },
      // modal
      hideModal() {
        this.$refs.modalRemove.hide();
      },
      confirmRemoveTask() {
        // tirando do array
        this.tasks.splice(this.taskSelected.index, 1);   
        // salvando no localStore 
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
        this.hideModal()
      }
    },
    computed: {
      isTasksEmpty() {
        return this.tasks.length === 0;
      }
    }
  }
</script>

<style scoped>
.empty{
  height: calc(100vh - 100px);
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: center;
}
  .empty-data {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
  }
  .empty-data-image {
    width: 500px;
    height: 500px;
  }
</style>