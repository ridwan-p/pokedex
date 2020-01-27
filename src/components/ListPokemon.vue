<template>
	<div class="card">
		<div class="card-body">
			<div class="row">
				<div class="col-md-12 d-flex justify-content-between">
					<h5>List Pokemon</h5>
					<div>
						<button class="btn btn-light mx-1" @click="refresh"><i class="fa fa-refresh"></i></button>
						<button class="btn btn-light" @click="showFilter"><i class="fa fa-filter"></i></button>
					</div>
				</div>
				<div class="col-md-12 mt-3" v-show="isFilter">
					<filter-template :refresh="isRefresh"  @filter="setList"></filter-template>
				</div>

				<div v-if="!isLoading" v-for="item in list" class="col-lg-4 col-md-6 col-sm-12 my-3">
					<card-pokemon :item="item"></card-pokemon>
				</div>
				<div class="col-md-12 text-center my-3" v-show="isLoading"><div class="spinner-border text-primary"></div></div>
			</div>
		</div>
	</div>
</template>

<script>
	import axios from 'axios'
	import { URL_API } from "../constants/pokemon"
	import CardPokemon from './CardPokemon'
	import FilterTemplate from './FilterTemplate'

	export default {
		components: {
			CardPokemon,
			FilterTemplate
		},

		mounted: function() {
			window.addEventListener('scroll', () => {
	      this.loadDataBottom()
	    })
	    this.isLoading = true
	    this.getListPokemon(this.url_next)
		},

		data: function() {
			return {
				items:[],
				url_next : `${URL_API}/pokemon`,
				isLoading: false,
				total: 0,
				isFilter: false,
				isRefresh: false,
			}
		},

		computed: {
			list: function() {
				return this.items;
			}
		},

		methods: {
			getListPokemon : function(url) {
				// this.isLoading = true
				return axios.get(url)
					.then(res => {
						this.items = [...this.items, ...res.data.results]
						this.url_next = res.data.next
						this.total = res.data.count
						this.isLoading = false
						return res;
					})
			},

			setList: function(url) {
				return url ? this.filterPokemon(url) : this.getListPokemon(this.url_next)
			},

			filterPokemon: function(url) {
				this.isLoading = true
				return axios.get(url)
					.then(res => {
						const { pokemon } = res.data
						let results = []
						let count = 0
						for(const i in pokemon) {
							results.push(pokemon[i].pokemon)
							count++
						}
						this.items = results
						this.url_next = null
						this.total = count
						this.isLoading = false
						return res;
					})
			},

			bottomVisible() {
	      const scrollY = window.scrollY
	      const visible = document.documentElement.clientHeight
	      const pageHeight = document.documentElement.scrollHeight
	      const bottomOfPage = visible + scrollY >= pageHeight
	      return bottomOfPage || pageHeight < visible
	    },

	    loadDataBottom() {
	    	if(this.bottomVisible() && !!this.url_next) {
	    		this.getListPokemon(this.url_next)
	    	}
	    },

	    showFilter() {
	    	this.isFilter = !this.isFilter
	    },

	    refresh() {
	    	this.items = [],
				this.url_next = `${URL_API}/pokemon`,
				this.isLoading =  false
				this.total =  0
				this.isRefresh = true
	    	this.getListPokemon(`${URL_API}/pokemon`).then(res => {
	    		this.isRefresh = false;
	    	})
	    }
		}
	}
</script>