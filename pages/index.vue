<template>
<div class="container">
  <b-input-group size="lg">
	<template v-slot:prepend>
      <b-input-group-text>URL</b-input-group-text>
    </template>
    <b-form-input v-model="url" type="url"></b-form-input>
	<b-button variant="success" v-on:click="addClick">Add</b-button>
  </b-input-group>
  <b-input-group size="lg">
	<template v-slot:prepend>
      <b-input-group-text>Query</b-input-group-text>
    </template>
    <b-form-input v-model="query" type="text"></b-form-input>
	<b-button variant="success" v-on:click="searchClick">Search</b-button>
  </b-input-group>
  <h3>追加されたリンク一覧</h3>
  <ul v-for="text in texts">
    <li>
	  {{text.title}}({{ text.url }})
	</li>
  </ul>
</div>
</template>

<script>
export default {
	data: () => ({
		texts: [],
		url: "",
		query: ""
	}),
	methods: {
		addClick: function(){
			let searchInput = document.getElementById("search-input");
			if(!this.url){
				return;
			}
			let body = 'url=' + this.url;
			fetch("/api/v1/text", {
				method: "POST",
				headers: {
					"Content-Type": "application/x-www-form-urlencoded",
				},
				body: body
			}).then(resp => console.log(resp))
			.then(r => this.url = "");
			this.syncTexts();
		},
		searchClick: function(){
			fetch("/api/v1/text?q=" + this.query)
				.then(resp => resp.json())
				.then(json => (this.texts = json.data));
		},
		syncTexts: function(){
			fetch("/api/v1/text")
				.then(resp => resp.json())
				.then(json => (this.texts = json.data));
		}
	},
	mounted: function() {
		setInterval(() => this.syncTexts(), 10000);
	}
}
</script>

<style>

</style>
