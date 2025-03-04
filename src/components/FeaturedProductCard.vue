<template>
  <div
      class="group relative bg-white/5 rounded-xl overflow-hidden hover:bg-white/10 transition-all duration-500 transform perspective-1000"
      :class="{ 'hover:-rotate-y-180': isFlipped }"
  >
    <div class="absolute inset-0 bg-gradient-to-br from-black/20 to-transparent opacity-50"></div>
    <div class="relative z-10 p-6 transition-transform duration-700 transform-style-3d backface-hidden" :class="{ '-rotate-y-180': isFlipped }">
      <div class="aspect-[3/4] relative mb-6 group-hover:scale-105 transition-transform duration-500">
        <div class="absolute inset-0 bg-gradient-to-br from-[#051810] to-transparent opacity-20 rounded-lg"></div>
        <img
            :src="product.image"
            :alt="product.name"
            class="w-full h-full object-cover rounded-lg shadow-xl"
            loading="lazy"
        />
        <div
            class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/30 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300"
        ></div>
      </div>
      <div class="text-center">
        <h3 class="text-xl font-kashmir mb-2" :class="product.colorClass">{{ product.name }}</h3>
        <p class="text-white/90 text-sm mb-4">{{ product.description }}</p>
        <p class="text-xs text-white/60 mb-4">{{ product.type }}</p>
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
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { ShoppingBagIcon } from 'lucide-vue-next'

const props = defineProps({
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

const isFlipped = ref(false)
</script>

<style scoped>
.perspective-1000 {
  perspective: 1000px;
}

.transform-style-3d {
  transform-style: preserve-3d;
}

.backface-hidden {
  backface-visibility: hidden;
}

.-rotate-y-180 {
  transform: rotateY(180deg);
}
</style>

