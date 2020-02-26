<template>
<div class="container">
  <ul v-for="text in texts">
    <li>
	  {{ text.filepath }} => {{ text.contents }}
	</li>
  </ul>
</div>
</template>

<script>
export default {
	data: () => ({
		texts: []
	}),
	methods: {

	},
	mounted: function() {
		fetch("/api/v1/text")
			.then(resp => resp.json())
			.then(json => (this.texts = json.data));
	}
}
</script>

<style>

</style>
