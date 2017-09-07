<template>
	<div class="ProductList">
         
         <div class="row">

            <div class="col-sm-12 col-xs-12">
               <search-add @products-add="showModal" @products-filter="filteredList"></search-add>
            </div><!-- search-add component-->

  
          </div>
         
	
	   	 <table class="table  table-striped"><!-- table list -->
	   	 	<thead>
	   	 		<tr>
	   	 			<th class="text-center">Name <span class="float-right" v-bind:class="{'triangle-down' : sortDown, 'triangle-up' : sortUp}" @click="sortList()"></span></th>
	   	 			<th class="text-center">Price <span class="float-right" v-bind:class="{'triangle-down' : sortDown, 'triangle-up' : sortUp}" @click="sortList()"></span></th>
	   	 			<th class="text-center">Actions</th>
	   	 		</tr>
	   	 	</thead>
	   	 	<tbody>
	   	 		<tr v-for="(product, index) in filteredProducts">
	   	 			<td>
	   	 				<a href="#">{{ product.name }}</a>
	   	 				<span class="badge badge-pill badge-secondary float-right">{{ product.count }}</span>
	   	 			</td>
	   	 			<td class="text-center">
	   	 			   {{ product.price | currency }}
	   	 			</td>
	   	 			<td class="text-center">
	   	 			  <button class="btn btn-secondary" @click="showEditModal(product.id)">Edit</button>
	   	 			  <button class="btn btn-danger" @click="showDeleteModal(product.id,index)">Delete</button>
	   	 			</td>
	   	 		</tr>
	   	 	</tbody>
	   	 </table><!-- table list -->

         <div class="modal-shadow" v-if="show || deleteModal"></div><!-- modal back -->
	   	 <div class="modal" role="dialog" v-if="deleteModal"><!-- delete product - modal -->
	   	 	<div class="modal-dialog" role="document">
	   	 		<div class="modal-content">
	   	 			<div class="modal-header">
	   	 			    <h4>Delete product "{{ delItemTitle }}" ?</h4>
	   	 				<button type="button" class="close" @click="hideDeleteModal()"><span aria-hidden="true" aria-label="Close">&times;</span></button>
	   	 			</div>
	   	 			<div class="modal-body">
	   	 				<button type="button" class="btn btn-success" @click="deleteProduct()">Yes</button>
	   	 				<button type="button" class="btn btn-danger" @click="hideDeleteModal()">No</button>
	   	 			</div>
	   	 		</div>
	   	 	</div>
	   	 </div><!-- delete product - modal -->

	   	 	 <!-- edit product - modal -->
	   	 	<div class="modal animated" v-bind:class="{'bounceInDown': btnAdd ,'bounceInLeft': btnUpdate}" role="dialog" v-if="show">
				<div class="modal-dialog" role="document">
					<div class="modal-content">
						<div class="modal-header">
							<h4  v-if="btnAdd" class="modal-title">Add new product</h4>
							<h4 v-if="btnUpdate" class="modal-title">Edit product</h4>
							<button type="button" class="close" @click="hideModal()"><span aria-hidden="true" aria-label="Close">&times;</span></button>
						</div>
						<div class="modal-body">
						   <div class="form-group">
							<label  for="name">Name: ({{ lengthName }}/15) <small class="text-danger" v-if="lengthName > 15">Name is too long</small></label>
							<input class="form-control" type="text" name="name"  @blur="inputBlur('name')" v-bind:class="{'is-invalid': invalidName}" v-model.trim="name" id="name">
							    <small class="text-danger" v-if="invalidName">Enter product name</small>
						   </div>
						   <div class="form-group">
						   	<label for="count">Count:</label>
						   	<input class="form-control" type="number" name="count" v-on:keyup="keymonitorCount" @blur="inputBlur('count')" v-bind:class="{'is-invalid': invalidCount}" v-model.number="count" id="count">
						   	 <small class="text-danger" v-if="invalidCount">Enter the quantity of the product</small>
						   </div>
						   <div class="form-group">
						   	 <label for="price">Price: {{ price | currency }}</label>
						   	 <input class="form-control" type="number" name="price" v-on:keyup="keymonitorPrice" @blur="inputBlur('price')" v-bind:class="{'is-invalid': invalidPrice}"  v-model.number="price" id="price">
						   	  <small class="text-danger" v-if="invalidPrice">Enter product price</small>
						   </div>
						</div>
						<div class="modal-footer">
							<button type="button" class="btn btn-info float-left" v-show="lengthName <= 15" v-if="btnAdd" @click="addProduct()">Add</button>
							<button type="button" class="btn btn-info float-left" v-show="lengthName <= 15" v-if="btnUpdate" @click="updateProduct()">Update</button>
						</div>
					</div>
				</div><!-- modal-dialog -->
			</div>
	   	 <!-- edit product - modal -->
	   

	</div>

</template>

<script>

import Vue from 'vue'
import SearchAdd from './SearchAdd'
import VueLocalStorage from 'vue-ls'

let options = {
	namespace: 'vuejs__'
}
Vue.use(VueLocalStorage,options)


	export default{
		name: 'products-list',
		components: {
			SearchAdd
		},
		data() {
			return {
				products: [],
				deleteModal: false,
				prodIndex: null,
				updateIndex: null,
				show: false,
				invalidName: false,
				invalidCount: false,
				invalidPrice: false,
				name: '',
			    count: null,
			    price: null,
			    id: null,
				btnAdd: false,
				btnUpdate: false,
				sortUp: false,
				sortDown: true,
				search: '',
				delItemTitle: '',
				itemIndex: null
			}
		},
		mounted(){
			  
			this.products = Vue.ls.get('products');
		
		},
		methods: {

			
			 showDeleteModal(prodIndex,arrIndex) {
			 	
			 	this.deleteModal = true;
			 	this.prodIndex = prodIndex;
			 	this.itemIndex = arrIndex;

			 	var product = this.products.map((item,i) => {
			 	      return (item.id === prodIndex) ? this.delItemTitle = item.name : ''  
			 	});
			
			 },
			 hideDeleteModal() {
			 	this.deleteModal = false;
			 	this.prodIndex = null;
			 },
			 deleteProduct() {
                    
			 	var index = this.products.findIndex((item) => {
			 		 return item.id == this.prodIndex
			 	});

			 	 this.products.splice(index,1);

			 	 Vue.ls.set('products',this.products);
			 	 this.hideDeleteModal();
			 },
			showModal(action) {
				(action === 'add') ? this.btnAdd = true : this.btnAdd = false;
				(action === 'update') ? this.btnUpdate = true : this.btnUpdate = false;

				this.show = true;
			},
			hideModal() {
				this.name = '';
				this.count = null;
				this.price = null;
                 
                this.invalidName = false;
                this.invalidCount = false;
                this.invalidPrice = false;

				this.updateIndex = null;

				this.show = false;
			},
			inputBlur(inputName) {
                if(inputName == 'name'){
                	(this.name === '') ? this.invalidName = true : this.invalidName = false;
                }
                if(inputName == 'count'){
                	(this.count === null || this.count === '') ? this.invalidCount = true : this.invalidCount = false;
                }
				if(inputName == 'price'){
					(this.price === null || this.price === '') ? this.invalidPrice = true : this.invalidPrice = false;
				}
				
			},
			addProduct() {


				 (this.name === '') ? this.invalidName = true : this.invalidName = false;
				 (this.count === null || this.count === '') ? this.invalidCount = true : this.invalidCount = false;
				 (this.price === null  || this.price === '') ? this.invalidPrice = true : this.invalidPrice = false;

				if(this.name !== '' && (this.count !== null && this.count !== '') && (this.price !== null && this.price !== '')) {
                   
                    
                    this.price = String(this.price);
                  
		
				 	this.products = this.products || [];
				 	this.products.push({
				 		name: this.name,
					 	count: this.count,
					 	price: this.price,
					 	id: Math.random()
				 	});
				 
                    Vue.ls.set('products',this.products);
                     
	                 this.name = '';
		             this.count = null;
		             this.price = null;

		             this.hideModal();

				 }

              
		     },
			 showEditModal(index) {

			 	 this.showModal('update'); 
                 
                 this.updateIndex = index;
			 
			 	 var product = this.products.filter((item) => {
			 	 	  return (item.id === index) ? item : ''
			 	 });
			 	 
			 	 this.name = product[0].name;
			 	 this.count = product[0].count;
			 	 this.price = product[0].price;
			 	
			 },
			 updateProduct() {

			 	 this.products.filter((item) => {
			 	 	   if(item.id === this.updateIndex){

			 	 	     item.name = this.name;
			 	 	   	 item.count = this.count;
			 	 	   	 item.price = this.price;
						   	   
			 	 	   }
			 	 	  
			 	 });
			 
			 	 Vue.ls.set('products',this.products);
			 	 this.hideModal();
			 },
			 sortList(){
			 	this.sortDown = !this.sortDown;
			 	this.sortUp = !this.sortUp;
			 
			 	this.products.reverse();
			 },
			 filteredList(search) {
			 	 this.search = search;
	
			 },
			 keymonitorCount(e) {
				 (e.key === 'e' || e.key === 'E') ? this.count = null : null
			 },
			 keymonitorPrice(e) {
			 	 (e.key === 'e' || e.key === 'E') ? this.price = null: null
			 }
			
		},

		computed: {
			filteredProducts() {

				this.products = this.products || [];
				return this.products.filter((product) => {
					 return product.name.toUpperCase().match(this.search.toUpperCase())
				})

			},
			lengthName() {

				return this.name.length;
				(this.lengthName > 15) ? this.invalidName = true : this.invalidName = false;
			}

		}
	}
</script>