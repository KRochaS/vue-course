<template>
    <div class="container">
        <h1>Vue.js + Github</h1>
        <p class="lead">
            Página que lista issues de um repositório do Github, usando Vue.js.
        </p>

        <div v-if="response.status == 'error'" class="alert alert-danger">
           {{ response.message }}
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
                <tr v-if="showIssues" v-for="issue in issues" :key="issue.number">
        
                    <td> 
                      
                        <router-link  :to="{name: 'GitHubIssue', params: {name: username, repo: repository, issue: issue.number}}"> {{issue.number}}</router-link> 
            
                        <!-- <a  @click.prevent.stop="getIssue(issue)" href="#">  {{issue.number}} </a>  <img v-if="issue.is_loading" src="../assets/loading.svg" height="15px"></td> -->
                      <td> {{issue.title}} </td>
                </tr>
            <!-- <tr>
                <td class="text-center" colspan="2"><img src="/static/loading.svg" alt=""></td>
            </tr> -->

            <tr v-if="noIssues">
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

  created() {
    this.getLocalData();
  },

  data() {
    return {
      username: "",
      repository: "",
      issues: [],
      selectedIssue: {},
      loading: false,
      loadingIssue: false,
      response: {
          status: '',
          message: '',
      }
    };
  },

  computed: {
    // roda sempre que as variáveis internas se alterarem
    // bom uso para de condição deixando o html mais limpo com a condição no computed

    showIssues() {
      return this.issues.length > 0 && !this.loading;
    },
    noIssues() {
      return this.issues.length == 0 && !this.loading;
    },
  },

    methods: {
      reset() {
        this.username = "";
        this.repository = "";
        localStorage.removeItem('gitHubIssues'); // Limpar LocalStorage
      },

      resetResponse() {
          this.response.status = '';
          this.response.message = '';
      },

      async getIssues() {
          this.resetResponse();
           this.issues = [];
        console.log("Get Issues");

        try {
          this.loading = true;
          if (this.username && this.repository) {
            localStorage.setItem(
              "gitHubIssues",
              JSON.stringify({
                username: this.username,
                repository: this.repository
              })
            );
            // set seta os valores do local storage - salvando no Local Storage chave  ,  valor;
            const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues`;
            let resp = await axios.get(url);
            console.log("resp", resp.data);
            setTimeout(() => {
              this.issues = resp.data;

              this.loading = false;
            }, 1000);
          }
        } catch (error) {
          console.log("Erro ao buscar Issues", error);
          this.loading = false;
          this.response.status = 'error';
          this.response.message = 'Repositório não existe';
           this.issues = [];
        }
      },

      getLocalData() {
        const localData = JSON.parse(localStorage.getItem("gitHubIssues")); // recupera os dados do localstorage
        console.log("local data", localData);
        if (localData && localData.username && localData.repository) {
          this.username = localData.username;
          this.repository = localData.repository;
          console.log("repository", this.repository);
          this.getIssues();
        }
      }
    }
  }

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
</script>
<style>
img {
  height: 22px;
}
</style>
