<script setup>
import { ref, watch } from 'vue'
import { useDebounceFn } from '@vueuse/core'

const searchTerm = ref('')

const products = { isLoading: false, data: [] }

async function fetchProducts(term) {
  const res = await fetch(`https://dummyjson.com/products/search?q=${term}`)
  return (await res.json()).products
}

const findProducts = async newTerm => {
  if (newTerm) {
    products.data = await fetchProducts(newTerm)
    products.isLoading = false
    console.log(products)
  }
}

watch(searchTerm, () => {
  console.log(products)
  products.isLoading = true
})

watch(searchTerm, useDebounceFn(findProducts, 300))
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
    <p v-if="products.isLoading">Loading...</p>
    <ul class="list-disc">
      <li v-for="product of products.data">
        {{ product.title }}
      </li>
    </ul>
  </div>
</template>
