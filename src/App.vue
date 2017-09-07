<template>
  <div id="app" class="container">
    <div class="row">
      <div class="col-sm-12">
            <product-list></product-list>
      </div>
    </div>


  </div>
</template>

<script>
import Vue from 'vue'
import accounting from 'accounting-js'
import ProductList from './components/ProductList'

import BootstrapVue from 'bootstrap-vue'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

Vue.use(BootstrapVue);



Vue.filter('currency',function(val){
     return accounting.formatMoney(val);
})

export default {
  name: 'app',
  components: {
     ProductList
  }
}
</script>

<style src="./assets/animate.css"></style>
<style src="./assets/style.css"></style>
