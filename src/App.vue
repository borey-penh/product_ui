<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const products = ref([])

const name = ref("")
const price = ref("")
const editId = ref(null)

// GET
function getProducts() {
  axios.get('http://127.0.0.1:8000/api/products')
    .then(res => {
      // Support both array response and { data: [...] }
      products.value = Array.isArray(res.data) ? res.data : (res.data?.data ?? [])
    })
    .catch(err => {
      console.error('GET /api/products failed:', err)
      products.value = []
    })
}

// ADD or UPDATE
function saveProduct() {
  if (editId.value) {
    // UPDATE
    axios.put(`http://127.0.0.1:8000/api/products/${editId.value}`, {
      name: name.value,
      // send as number if backend expects numeric price
      price: Number(price.value)
    }).then(() => {
      resetForm()
      getProducts()
    }).catch(err => {
      console.error('PUT /api/products failed:', err)
    })
  } else {
    // CREATE
axios.post('http://127.0.0.1:8000/api/products', {
      name: name.value,
      price: Number(price.value)
    }).then(() => {
      resetForm()
      getProducts()
    }).catch(err => {
      console.error('POST /api/products failed:', err)
    })
  }
}

// DELETE
function deleteProduct(id) {
  axios.delete(`http://127.0.0.1:8000/api/products/${id}`)
    .then(() => {
      getProducts()
    })
}
  
// EDIT (fill form)
function editProduct(p) {
  name.value = p.name
  price.value = p.price
  editId.value = p.id
}

// RESET FORM
function resetForm() {
  name.value = ""
  price.value = ""
  editId.value = null
}

onMounted(() => {
  getProducts()
})
</script>

<template>
  <div class="container">
    <h1>Product Management</h1>

    <div class="form">
      <input v-model="name" placeholder="Product name" />
      <input v-model="price" placeholder="Price" />

      <button @click="saveProduct">
        {{ editId ? 'Update Product' : 'Add Product' }}
      </button>
    </div>

    <h2>Products</h2>

    <ul>
      <li v-for="p in products" :key="p.id">
        <span>{{ p.name }} - ${{ p.price }}</span>

        <div>
          <button @click="editProduct(p)">Edit</button>
          <button class="delete" @click="deleteProduct(p.id)">Delete</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.container {
  max-width: 500px;
  margin: auto;
  font-family: Arial;
}

input {
  display: block;
  width: 100%;
  margin: 10px 0;
  padding: 10px;
}

button {
  padding: 8px 12px;
  margin-right: 5px;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 6px;
}

.delete {
  background: #e74c3c;
}

li {
  display: flex;
  justify-content: space-between;
  margin: 10px 0;
  background: #f4f4f4;
  padding: 10px;
  border-radius: 6px;
}
</style>