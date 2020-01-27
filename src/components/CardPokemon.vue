<template>
	<div class="card text-center shadow">
		<div v-if="isLoading" class="card-body" style="height: 300px"></div>
		<div v-else class="card-body">
			<h5 class="card-title">{{ pokemon.name || '' }}</h5>
			<img :src="pokemon.sprites && pokemon.sprites.front_default ? pokemon.sprites.front_default: dummy " alt="pokemon image" width="100">
			<div>
				<span v-for="type in pokemon.types" class="badge badge-dark mx-1">{{type.type.name}}</span>
			</div>
			<table class="table mt-4 mb-0 borderless">
				<tbody>
					<tr class="text-dark">
						<td class="pb-0">{{pokemon.height || ''}} Inch</td>
						<td class="pb-0">{{pokemon.weight || ''}} Kg</td>
						<td class="pb-0" style="text-transform: capitalize;">{{pokemon.species ? pokemon.species.name : ''}}</td>
					</tr>
					<tr class="font-14 text-secondary">
						<td>Height</td>
						<td>Weight</td>
						<td>Species</td>
					</tr>
				</tbody>
			</table>
		</div>
	</div>
</template>

<script>
	import axios from 'axios'
	import { URL_API } from "../constants/pokemon"
	import dummy from '../assets/dummy.png'

	export default {
		props: ["item"],

		mounted: function() {
			this.getPokemon(this.item.url)
		},

		data: function() {
			return {
				pokemon:{},
				isLoading: false,
				dummy:dummy
			}
		},
		methods: {
			getPokemon: function(url) {
				this.isLoading= true
				return axios.get(url)
					.then(res => {
						this.pokemon = res.data
						this.isLoading = false
						return res
					})
			}
		}
	}
</script>

<style scoped>
	.card-title {
		text-transform: capitalize;
	}
	.borderless td, .borderless th {
    border: none;
	}
	.font-14 {
		font-size: 14px;
	}
</style>