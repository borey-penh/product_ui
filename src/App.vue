<script setup>

import { ref, onMounted } from 'vue'

import axios from 'axios'


const products = ref([])


const name = ref("")

const price = ref("")



function getProducts() {

  axios.get(
    'http://127.0.0.1:8000/api/products'
  )

    .then(res => {

      products.value = res.data

    })

}



function addProduct() {


  axios.post(

    'http://127.0.0.1:8000/api/products',

    {
      name: name.value,
      price: price.value
    }

  )

    .then(() => {

      name.value = ""
      price.value = ""

      getProducts()

    })


}



onMounted(() => {

  getProducts()

})


</script>


<template>

  <h1>
    Product Management
  </h1>


  <input v-model="name" placeholder="Product name" />


  <input v-model="price" placeholder="Price" />


  <button @click="addProduct">

    Add Product

  </button>



  <h2>Products</h2>


  <ul>

    <li v-for="p in products">

      {{ p.name }}
      -
      ${{ p.price }}

    </li>


  </ul>


</template>

<style scoped>
  
</style>