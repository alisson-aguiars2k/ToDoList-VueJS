<template>
  <div class="container mt-2">
    <b-form>
      <!-- 1- group -->
      <b-form-group
      label="Título"
      label-for="subject">

        <b-form-input
        id="subject"
        v-model.trim="$v.form.subject.$model"
        type="text"
        placeholder="Ex: Estudar VueJS"
        autocomplete="off"
        required
        :state="getValidation"
        aria-describedby="subject-feedback">
        </b-form-input>
        <b-form-invalid-feedback id="subject-feedback">
          Este campo é obrigatório, por favor degite um 'Título'
        </b-form-invalid-feedback>
        <!-- 2- group -->
      </b-form-group>
      <b-form-group
      label="Descrição"
      label-for="description">

        <b-form-textarea
        id="description"
        v-model="form.description"
        type="text"
        placeholder="Ex: preciso estudar VueJS às 15hrs"
        autocomplete="off"
        required>
        </b-form-textarea>
        
      </b-form-group>
      <b-button 
      type="submit" 
      variant="outline-primary" 
      @click="saveTask"
      :disabled="!getValidation"
      >Salvar</b-button>
    </b-form>
   
  </div>
</template>

<script>
  import ToastMixin from '@/mixins/toastMixin.js';
  import { required, minLength } from "vuelidate/lib/validators";

  export default {
    name:'Form',

    mixins: [ToastMixin],

    data() {
      return {
        form: {
          subject:'',
          description:''
        },
        methodSave: "new"
      }
    },
    validations: {
      form: {
        subject: {
          required,
          minLength: minLength(3)
        }
      }
    },
    created() {
      if(this.$route.params.index === 0 || this.$route.params.index !== undefined){
        this.methodSave = "update";
        let tasks = JSON.parse(localStorage.getItem("tasks"));
        this.form = tasks[this.$route.params.index];
      } 
    },
    methods: {
      // salvando as alterações
      saveTask() {
        if(this.methodSave === "update") {
          let tasks = JSON.parse(localStorage.getItem("tasks"));
          tasks[this.$route.params.index] = this.form;
          // salvando no localStorage
          localStorage.setItem("tasks", JSON.stringify(tasks));
          // Toast update
          this.showToast("success", "Sucesso", "Tarefa atualizada com sucesso.")
          // redirecionando a rota para list
          this.$router.push({name:'list'})
          return
        }
        // salvando no array
        let tasks = (localStorage.getItem("tasks")) ? JSON.parse(localStorage.getItem("tasks")) : [];
        tasks.push(this.form);
        // salvando no localStorage
        localStorage.setItem("tasks", JSON.stringify(tasks));
        // Toast create
        this.showToast("success", "Sucesso", "Tarefa criada com sucesso.")
        // direcionando a rota para list
        this.$router.push({name:'list'})
      }
    },
    computed: {
    getValidation() {
      if(this.$v.form.subject.$dirty === false) {
        return null;
      }

      return !this.$v.form.subject.$error;
    }
  }
  }
</script>
