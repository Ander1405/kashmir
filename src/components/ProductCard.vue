<template>
  <div
      class="group relative bg-white/5 rounded-lg overflow-hidden hover:bg-white/10 transition-all duration-500 transform hover:scale-[1.02] mb-10"
  >
    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/30 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
    <img
        :src="product.image"
        :alt="product.name"
        class="w-full h-56 sm:h-48 object-cover transform group-hover:scale-105 transition-transform duration-500"
        loading="lazy"
    />
    <div class="p-4 flex flex-col justify-between min-h-[11rem]">
      <div class="flex flex-col h-full">
        <h3 class="text-lg font-kashmir mb-2" :class="product.colorClass">{{ product.name }}</h3>
        <p class="text-white/90 text-sm mb-2 flex-grow">{{ product.description }}</p>
        <p class="text-xs text-white/60 mb-4">{{ product.type }}</p>
      </div>
      <div class="flex justify-between items-center">
        <p class="text-lg font-bold">{{ formatPrice(product.price) }}</p>
        <button
            @click="$emit('add-to-cart', product)"
            class="bg-white/10 hover:bg-white/20 text-white px-4 py-2 rounded-full transform hover:scale-105 transition-all duration-300 flex items-center space-x-2"
        >
          <ShoppingBagIcon class="h-4 w-4" />
          <span>{{ t('products.addToCart') }}</span>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ShoppingBagIcon } from 'lucide-vue-next'

defineProps({
  product: {
    type: Object,
    required: true
  },
  t: {
    type: Function,
    required: true
  },
  formatPrice: {
    type: Function,
    required: true
  }
})
</script>
