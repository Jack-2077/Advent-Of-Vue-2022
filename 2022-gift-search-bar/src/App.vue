<script setup>
import { ref, watch } from 'vue'
import { useDebounceFn } from '@vueuse/core'

const searchTerm = ref('')

let products = ref([])
let isLoading = ref(false)

async function fetchProducts(term) {
  const res = await fetch(`https://dummyjson.com/products/search?q=${term}`)
  return (await res.json()).products
}

const findProducts = async newTerm => {
  if (newTerm) {
    products.value = await fetchProducts(newTerm)
    isLoading.value = false
  }
}

watch(products, newProducts => {
  if (newProducts.length === 0) {
    alert('No product found')
  }
})

watch(searchTerm, () => {
  isLoading.value = true
})

watch(searchTerm, useDebounceFn(findProducts, 1000))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input
      type="text"
      class="p-2 border-2 border-gray-dark"
      v-model="searchTerm"
      placeholder="Start typing..."
      on-change="findProducts"
    />
    <p v-if="isLoading">Loading...</p>
    <ul v-else class="list-disc">
      <li v-for="product of products">
        {{ product.title }}
      </li>
    </ul>
  </div>
</template>
