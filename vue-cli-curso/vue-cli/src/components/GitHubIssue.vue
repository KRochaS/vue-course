<template>
	<div class="container">
		<div v-if="loadingIssue">
			<img src="../assets/loading.svg">
		</div>
		<div v-if="!loadingIssue && issue.number">
			<h2>Issue #{{issue.number}} - {{ issue.title }}</h2>
			<div>{{issue.body}}</div>
			<br>
			<a href="javascript:history.go(-1)" class="btn btn-primary">Voltar</a>
			<!-- javascript:history.go(-1) é a função de voltar do browser -->
		</div>
	</div>
</template>

<script>
import axios from "axios";

export default {
	name: "GitHubIssue",

	created() {
		// depois que o componente for criado chama o getIssue()
		this.getIssue();
	},

	data() {
		return {
			issue: {},
			loadingIssue: false
		};
	},

	methods: {
		async getIssue() {
			try {
				this.loadingIssue = true;

				this.loadingIssue = true;
				const url = `https://api.github.com/repos/${this.$route.params.name}/${this.$route.params.repo}/issues/${this.$route.params.issue}`;
				let resp = await axios.get(url);
				setTimeout(() => {
					this.issue = resp.data;

					this.loadingIssue = false;
				}, 1000);
			} catch (error) {
				console.log("Erro ao buscar Issue", error);
				this.loadingIssue = false;
			}
		},

		voltar() {
			this.issue = {};
		}
	}
};
</script>


<style>
	img {
	height: 22px;
	}
</style>