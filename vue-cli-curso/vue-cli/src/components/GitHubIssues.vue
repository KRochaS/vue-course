<template>
    <div class="container">
        <h1>Vue.js + Github</h1>
        <p class="lead">
            Página que lista issues de um repositório do Github, usando Vue.js.
        </p>

        <div class="alert alert-danger">
           
        </div>

        <div class="row">
            <div class="col">
                <div class="form-group">
                    <input v-model="username" type="text" class="form-control" placeholder="github username">
                </div>
            </div>

            <div class="col">
                <div class="form-group">
                    <input v-model="repository" type="text" class="form-control" placeholder="github repositório">
                </div>
            </div>

            <div class="col-3">
                <div class="form-group">
                    <button class="btn btn-success" @click.prevent.stop="getIssues()">GO</button>
                    <button class="btn btn-danger" @click.prevent.stop="reset()">LIMPAR</button>
                </div>
            </div>
            
        </div>

        <br><hr><br>

        <!-- <template v-if="selectedIssue.id">
            <h2> {{ selectedIssue.title }} </h2>
            <div>
                {{selectedIssue.body}}
            </div>
            <br>

            <button @click.prevent.stop="voltar()" class="btn btn-primary"> Voltar </button> 
        </template> -->


        <!-- v-if="!selectedIssue.id" -->
        <table class="table table-sm table-bordered">
            <thead>
            <tr>
                <th width="100">Número</th>
                <th>Título</th>
            </tr>
            </thead>

            <tbody>
            <tr v-if="loading">
                <td class="text-center" colspan="2">
                    <img  src="../assets/loading.svg">
                    
                </td>
            

            </tr>
                <tr v-if="issues.length > 0 && !loading" v-for="issue in issues" :key="issue.number">
        
                    <td> 
                      
                        <router-link  :to="{name: 'GitHubIssue', params: {name: username, repo: repository, issue: issue.number}}"> {{issue.number}}</router-link> 
            
                        <!-- <a  @click.prevent.stop="getIssue(issue)" href="#">  {{issue.number}} </a>  <img v-if="issue.is_loading" src="../assets/loading.svg" height="15px"></td> -->
                      <td> {{issue.title}} </td>
                </tr>
            <!-- <tr>
                <td class="text-center" colspan="2"><img src="/static/loading.svg" alt=""></td>
            </tr> -->

            <tr v-if="issues.length == 0 && !loading">
                <td class="text-center" colspan="2">Nenhuma issue encontrada!</td>
            </tr>
            </tbody>
        </table>

        
    </div>
    




</template>

<script>
import axios from "axios";

export default {
    /* eslint-enable no-alert */
  name: "GitHubIssues",

  data() {
    return {
      username: "",
      repository: "",
      issues: [],
      selectedIssue: {},
      loading: false,
      loadingIssue: false,
    };
  },

  methods: {
    reset() {
      this.username = "";
      this.repository = "";
     
    },

    async getIssues() {
      console.log("Get Issues");


      try {
         this.loading = true;
        if (this.username && this.repository) {
          const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues`;
          let resp = await axios.get(url);
          console.log("resp", resp.data);
          setTimeout(() => {
            this.issues = resp.data;
         
            this.loading = false;

          }, 1000)
        }
      } catch (error) {
        console.log("Erro ao buscar Issues", error);
        this.loading = false;
      }
    },


    // async getIssue(issue) {

    //     try {
    //       this.loadingIssue = true;
    //       if (this.username && this.repository) {
    //           this.$set(issue, 'is_loading', true); // seta um novo valor dentro do objeto (adicionando o is_loading na issue)
    //           const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues/${issue.number}`;
    //           let resp = await axios.get(url);
    //           setTimeout(() => {
           
    //             this.selectedIssue = resp.data;
         
    //            this.$set(issue, 'is_loading', false);

    //         }, 1000)
    //       }
    //     } catch (error) {
    //         console.log('Erro ao buscar Issue', error);
    //         this.$set(issue, 'is_loading', false);
    //     }

    // },

    // voltar() {
    //     this.selectedIssue = {};
    // }
  }
};
</script>
<style>
img {
  height: 22px;
}
</style>
