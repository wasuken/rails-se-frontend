<template>
<div class="container">
  <b-form-group label="add type">
	<b-form-radio checked v-model="status" name="some-radios" value="single">url single</b-form-radio>
    <b-form-radio v-model="status" name="some-radios" value="deep">url deep</b-form-radio>
	<b-form-radio v-model="status" name="some-radios" value="zip">file zip</b-form-radio>
  </b-form-group>

  <div v-if="status === 'zip'">
	<b-input-group size="lg">
	  <template v-slot:prepend>
		<b-input-group-text>File</b-input-group-text>
      </template>
      <b-form-file
		v-model="file"
		:state="Boolean(file)"
		placeholder="Choose a file or drop it here..."
		drop-placeholder="Drop file here..."
		></b-form-file>
	  <b-button variant="success" v-on:click="fileUploadClick">Upload</b-button>
	</b-input-group>
  </div>

  <div v-else>
	<b-input-group size="lg">
	  <template v-slot:prepend>
		<b-input-group-text>URL</b-input-group-text>
      </template>
      <b-form-input v-model="url" type="url"></b-form-input>
	  <b-button variant="success" v-on:click="addClick">Add</b-button>
	</b-input-group>
  </div>

  <b-input-group size="lg">
	<template v-slot:prepend>
      <b-input-group-text>Query</b-input-group-text>
    </template>
    <b-form-input v-model="query" type="text"></b-form-input>
	<b-button variant="success" v-on:click="searchClick">Search</b-button>
  </b-input-group>

  <h3>追加されたリンク一覧</h3>
  <ul v-for="(text, i) in texts">
    <li>
	  <b-link href="javascript:void(0)" @click="$bvModal.show('modal' + i)">
		{{text.title}}
	  </b-link>
	  <b-modal :id="'modal' + i" :title="text.title">
		{{text.contents}}
	  </b-modal>
	</li>
  </ul>

  <hr/>


</div>
</template>

<script>
export default {
	data: () => ({
		texts: [],
		url: "",
		query: "",
		status: "single",
		file: null,
	}),
	methods: {
		addClick: function(){
			let searchInput = document.getElementById("search-input");
			if(!this.url){
				return;
			}
			let body = 'url=' + this.url;
			body += "&t=" + this.status;
			fetch("/api/v1/text", {
				method: "POST",
				headers: {
					"Content-Type": "application/x-www-form-urlencoded",
				},
				body: body
			}).then(resp => console.log(resp))
				.then(r => this.url = "")
				.then(r => this.syncTexts());
		},
		searchClick: function(){
			this.texts = [];
			fetch("/api/v1/text?q=" + this.query)
				.then(resp => resp.json())
				.then(json => {
					console.log(json);
					this.texts = json.data;
				});
		},
		syncTexts: function(){
			fetch("/api/v1/text")
				.then(resp => resp.json())
				.then(json => (this.texts = json.data));
		},
		fileUploadClick: function(){
			var formData = new FormData();
			formData.append('file', this.file);
			formData.append('t', this.status);
			fetch("/api/v1/text", {
				method: "POST",
				body: formData
			}).then(resp => console.log(resp))
				.then(r => this.file = null)
				.then(r => this.syncTexts());
		}
	},
	mounted: function() {
		setTimeout(() => this.syncTexts(), 3000);
	}
}
</script>

<style>

</style>
