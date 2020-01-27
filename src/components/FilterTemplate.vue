<template>
	<div class="card">
		<div class="card-body">
			<div class="row">
				<ul class="menu col-md-2 col-sm-12">
					<li v-for="type in types" class="menu-item">
						<button
					 		class="btn" 
							:class="type === filter ? 'btn-primary' : 'btn-light'" 
							@click="showFilter(type)"
							>{{type}}</button>
					</li>
				</ul>
				<div class="col-md-10 col-sm-12">
					<button v-for="item in items" @click="getFilterData(item)" class="btn btn-sm m-1" :class=" selected === item.name ? 'btn-primary': 'btn-outline-secondary'">{{ item.name }}</button>
					<button v-if="noMore" @click="showMore" class="btn btn-sm btn-secondary">Show More</button>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	import axios from 'axios'
	import { URL_API } from "../constants/pokemon"

	export default {
		props: ['refresh'],

		mounted:function() {
			this.showFilter('ability')
			this.selected = this.select
		},

		data: function() {
			return {
				items: [],
				isLoading: false,
				filter: null,
				total : 0,
				url_next: null,
				types: ['ability', 'type'],
				selected: ''
			}
		},

		watch: {
			refresh: function(newData) {
				if(newData) {
					this.selected = ''
				}
			}
		},

		computed : {
			noMore: function() {
				return !!this.url_next
			}
		},

		methods: {
			showFilter: function(filter, more = null) {
				this.filter = filter

				if(filter === 'all') return this.reset();

				return axios.get(more ? more : `${URL_API}/${filter}`)
					.then(res => {
						this.items= more ? [...this.items, ...res.data.results] : res.data.results
						this.total = res.data.count
						this.url_next = res.data.next
						return res
					})
			},
			showMore: function() {
				this.showFilter(this.filter, this.url_next)
			},
			getFilterData: function(item) {
				this.selected = item.name
				this.$emit('filter', item.url)
			},
			reset: function() {
				this.items = []
				this.filter = null
				this.total  = 0
				this.url_next = null
				this.getFilterData(null)
				return null
			}
		}
	}
</script>

<style scoped>
	.menu {
		list-style: none;
		border-right: 1px solid #eaeaea;
	}

	.menu .menu-item button {
		margin: .2rem .3rem;
		width: 100%;
		text-transform: capitalize;
	}

	@media (max-width: 576px) { 
		.menu {
			border-right: none;
		}
	}
</style>