<template>
	<div class="Search">
	     <div class="row">
		   <div class="col-sm-4 col-xs-4 block">
		   	  <input type="text" class="form-control" v-model="search"  placeholder="Search" />
		   </div>
		   <div class="col-sm-4 col-xs-2 block">
		   	   <button class="btn btn-info" @click="searchList()">Search</button>
		   </div>
		   <div class="col-sm-4 col-xs-4 block">
			   <button class="btn btn-info float-right" v-on:click="addModal('add')">Add New</button>
		   </div>
		 </div>
	</div>
</template>

<script>
	
  export default {
  	 name: 'search',
  	 data() {
			return {
				search: ''
			}
		},
		methods: {
			addModal(action) {
				this.$emit('products-add',action);
			},
			searchList() {
				if(this.search){
					this.$emit('products-filter',this.search);
				}
				
			}

		}
  }

  

</script>