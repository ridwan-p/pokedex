<template>
  <div id="app">
    <navbar></navbar>
    <list-pokemon class="container list-pokemon"></list-pokemon>
  </div>
</template>

<script>
import Navbar from './components/Navbar.vue'
import ListPokemon from './components/ListPokemon.vue'

export default {
  name: 'app',
  components: {
    Navbar,
    ListPokemon
  }
}
</script>

<style>
	@import "../node_modules/bootstrap/dist/css/bootstrap.min.css";

	.list-pokemon {
		margin-top: -100px;
	}
</style>
