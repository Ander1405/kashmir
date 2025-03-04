<template>
  <div class="min-h-screen overflow-hidden bg-[#0A2815] text-white">
    <!-- Promotional Modal -->
    <transition name="fade">
      <div v-if="showModal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm">
        <div class="bg-[#0A2815] p-8 rounded-2xl max-w-md mx-4 relative border border-orange-400/20">
          <button @click="closeModal" class="absolute top-4 right-4 text-white/60 hover:text-white">
            <XIcon class="h-6 w-6" />
          </button>
          <h3 class="text-2xl font-kashmir text-orange-400 mb-4">
            {{ t('promo.title') }}
          </h3>
          <p class="text-white/80 text-lg mb-6">
            {{ t('promo.description') }}
          </p>
          <button
              @click="closeModal"
              class="w-full bg-orange-400 text-black px-6 py-3 rounded-full hover:bg-orange-300 transform hover:scale-105 transition-all duration-300"
          >
            {{ t('promo.accept') }}
          </button>
        </div>
      </div>
    </transition>

    <!-- Shopping Cart Sidebar -->
    <transition name="slide-left">
      <div v-if="showCart" class="fixed inset-y-0 right-0 w-full md:w-96 bg-[#0A2815] shadow-xl z-40 border-l border-white/10">
        <div class="h-full flex flex-col p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-2xl font-kashmir text-orange-400">{{ t('cart.title') }}</h3>
            <button @click="toggleCart" class="text-white/60 hover:text-white">
              <XIcon class="h-6 w-6" />
            </button>
          </div>

          <div class="flex-1 overflow-auto">
            <div v-if="cart.length === 0" class="text-center text-white/60 py-8">
              {{ t('cart.empty') }}
            </div>
            <div v-else class="space-y-4">
              <div v-for="item in cart" :key="item.id" class="flex items-center gap-4 bg-white/5 p-4 rounded-lg">
                <img :src="item.image" :alt="item.name" class="w-20 h-20 object-contain" />
                <div class="flex-1">
                  <h4 class="font-kashmir text-white">{{ item.name }}</h4>
                  <p class="text-white/60">{{ formatPrice(item.price) }}</p>
                  <div class="flex items-center gap-2 mt-2">
                    <button
                        @click="updateQuantity(item.id, -1)"
                        class="text-white/60 hover:text-white"
                    >
                      <MinusIcon class="h-4 w-4" />
                    </button>
                    <input
                        type="number"
                        v-model.number="item.quantity"
                        @change="updateQuantityDirectly(item.id, item.quantity)"
                        class="w-12 bg-transparent text-center text-white border border-white/20 rounded"
                        min="1"
                    />
                    <button
                        @click="updateQuantity(item.id, 1)"
                        class="text-white/60 hover:text-white"
                    >
                      <PlusIcon class="h-4 w-4" />
                    </button>
                  </div>
                </div>
                <button
                    @click="removeFromCart(item.id)"
                    class="text-white/60 hover:text-white"
                >
                  <TrashIcon class="h-5 w-5" />
                </button>
              </div>
            </div>
          </div>

          <div class="border-t border-white/10 pt-4 mt-4">
            <div class="flex justify-between text-lg mb-4">
              <span class="text-white">{{ t('cart.total') }}</span>
              <span class="text-orange-400 font-bold">{{ formatPrice(cartTotal) }}</span>
            </div>
            <a
                :href="generateWhatsAppCartLink()"
                target="_blank"
                class="w-full bg-green-500 text-white px-6 py-3 rounded-full hover:bg-green-400 transform hover:scale-105 transition-all duration-300 flex items-center justify-center gap-2"
            >
              <MessageCircleIcon class="h-5 w-5" />
              <span>{{ t('cart.checkout') }}</span>
            </a>
          </div>
        </div>
      </div>
    </transition>

    <!-- Main Content -->
    <div class="bg-[#0A2815] text-white">
      <!-- Navigation -->
      <nav
          class="fixed w-full z-30 bg-[#0A2815]/90 backdrop-blur-sm border-b border-white/10"
          :class="{ 'nav-scrolled': scrolled }"
      >
        <div class="container mx-auto px-4 py-4">
          <div class="flex justify-between items-center">
            <a href="#" class="text-2xl font-kashmir text-orange-400">
              Kashmir
            </a>
            <div class="flex items-center gap-6">
              <div class="hidden md:flex space-x-8">
                <a v-for="item in menuItems"
                   :key="item.id"
                   :href="`#${item.id}`"
                   class="text-white/80 hover:text-orange-400 transition-colors duration-300">
                  {{ t(`menu.${item.id}`) }}
                </a>
              </div>
              <button
                  @click="toggleLanguage"
                  class="text-white/60 hover:text-white transition-colors duration-300 font-medium"
              >
                <span class="text-2xl">
                  {{ currentLanguage === 'es' ? '🇺🇸' : '🇪🇸' }}
                </span>
              </button>
              <button
                  @click="toggleCart"
                  class="relative text-white/60 hover:text-white transition-colors duration-300"
              >
                <ShoppingCartIcon class="h-6 w-6" />
                <span
                    v-if="cartItemsCount > 0"
                    class="absolute -top-2 -right-2 bg-orange-400 text-black text-xs w-5 h-5 rounded-full flex items-center justify-center"
                >
                  {{ cartItemsCount }}
                </span>
              </button>
            </div>
          </div>
        </div>
      </nav>

      <!-- Hero Section -->
      <section class="min-h-screen flex items-center justify-center pt-20 relative overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-b from-[#0A2815] via-black/50 to-[#0A2815]"></div>
        <div class="container mx-auto px-4 relative z-10">
          <div class="max-w-4xl mx-auto text-center">
            <h1 class="text-4xl md:text-6xl font-kashmir mb-6 text-orange-400">
              {{ t('hero.title') }}
            </h1>
            <p class="text-xl text-white/80 mb-8 leading-relaxed">
              {{ t('hero.subtitle') }}
            </p>
            <div class="flex justify-center space-x-4">
              <a
                  href="#productos"
                  class="bg-orange-400 text-black px-8 py-3 rounded-full hover:bg-orange-300 transform hover:scale-105 transition-all duration-300"
              >
                {{ t('hero.cta.products') }}
              </a>
              <a
                  href="https://wa.me/573217297970"
                  target="_blank"
                  class="bg-green-500 text-white px-8 py-3 rounded-full hover:bg-green-400 transform hover:scale-105 transition-all duration-300 flex items-center space-x-2"
              >
                <MessageCircleIcon class="h-5 w-5" />
                <span>{{ t('hero.cta.order') }}</span>
              </a>
            </div>
          </div>
        </div>
      </section>

      <!-- About Section -->
      <section id="nosotros" class="py-20 relative">
        <div class="container mx-auto px-4">
          <div class="max-w-4xl mx-auto text-center">
            <h2 class="text-3xl md:text-4xl font-kashmir mb-8 text-orange-400">{{ t('about.title') }}</h2>
            <p class="text-lg text-white/80 mb-12 leading-relaxed">
              {{ t('about.description') }}
            </p>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
              <div class="p-6 bg-white/5 rounded-lg hover:bg-white/10 transition-all duration-300">
                <ClockIcon class="h-12 w-12 text-orange-400 mx-auto mb-4" />
                <h3 class="text-xl font-semibold mb-2">{{ t('about.delivery.title') }}</h3>
                <p class="text-white/70">{{ t('about.delivery.description') }}</p>
              </div>
              <div class="p-6 bg-white/5 rounded-lg hover:bg-white/10 transition-all duration-300">
                <HeartIcon class="h-12 w-12 text-orange-400 mx-auto mb-4" />
                <h3 class="text-xl font-semibold mb-2">{{ t('about.quality.title') }}</h3>
                <p class="text-white/70">{{ t('about.quality.description') }}</p>
              </div>
              <div class="p-6 bg-white/5 rounded-lg hover:bg-white/10 transition-all duration-300">
                <UsersIcon class="h-12 w-12 text-orange-400 mx-auto mb-4" />
                <h3 class="text-xl font-semibold mb-2">{{ t('about.customers.title') }}</h3>
                <p class="text-white/70">{{ t('about.customers.description') }}</p>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Seasonal Products Section -->
      <section id="producto-estrella" class="py-20 relative overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-br from-[#051810] to-[#0A2815] opacity-50"></div>
        <div class="container mx-auto px-4 relative z-10">
          <div class="text-center mb-12">
            <h2 class="text-3xl md:text-4xl font-kashmir mb-4 text-orange-400">
              {{ t('seasonalProducts.title') }}
            </h2>
            <h3 class="text-xl text-white/90 mb-2">{{ t('seasonalProducts.subtitle') }}</h3>
            <p class="text-white/70 max-w-2xl mx-auto">
              {{ t('seasonalProducts.description') }}
            </p>
          </div>

          <div class="grid md:grid-cols-2 gap-8 max-w-5xl mx-auto">
            <FeaturedProductCard
                v-for="product in seasonalProducts"
                :key="product.id"
                :product="product"
                :t="t"
                :format-price="formatPrice"
                @add-to-cart="addToCart"
                class="transform transition-all duration-700 hover:translate-y-[-10px]"
                :class="{'translate-x-[-20px] md:translate-x-0': product.id === 'russian-cocaine',
                      'translate-x-[20px] md:translate-x-0': product.id === 'mexican-love'}"
            />
          </div>
        </div>
      </section>


      <!-- Products Section -->
      <section id="productos" class="py-20 relative">
        <div class="container mx-auto px-4">
          <h2 class="text-3xl md:text-4xl font-kashmir mb-12 text-orange-400 text-center">
            {{ t('products.title') }}
          </h2>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
            <transition-group name="fade-up" appear>
              <ProductCard
                  v-for="beer in beers"
                  :key="beer.id"
                  :product="beer"
                  :t="t"
                  :format-price="formatPrice"
                  @add-to-cart="addToCart"
              />
            </transition-group>
          </div>
        </div>
      </section>

      <!-- Contact Section -->
      <section id="contacto" class="py-20">
        <div class="container mx-auto px-4 text-center">
          <h2 class="text-3xl md:text-4xl text-orange-400 font-kashmir mb-8">{{ t('contact.title') }}</h2>
          <p class="text-white/80 mb-8 max-w-2xl mx-auto">
            {{ t('contact.description') }}
          </p>
          <a
              href="https://wa.me/573217297970"
              target="_blank"
              class="inline-flex items-center space-x-2 bg-green-500 text-white px-8 py-3 rounded-full hover:bg-green-400 transform hover:scale-105 transition-all duration-300"
          >
            <MessageCircleIcon class="h-5 w-5" />
            <span>{{ t('contact.cta') }}</span>
          </a>
        </div>
      </section>

      <!-- Footer -->
      <footer class="py-8 border-t border-white/10">
        <div class="container mx-auto px-4">
          <div class="flex justify-center">
            <div class="text-white/60">
              {{ t('footer.copyright') }}
            </div>
          </div>
        </div>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'
import {
  MenuIcon, XIcon, MessageCircleIcon, ChevronDownIcon,
  ClockIcon, HeartIcon, UsersIcon,
  ShoppingCartIcon, ShoppingBagIcon, PlusIcon, MinusIcon,
  TrashIcon
} from 'lucide-vue-next'
import ProductCard from "@/components/ProductCard.vue";
import FeaturedProductCard from "@/components/FeaturedProductCard.vue";

// State
const scrolled = ref(false)
const showModal = ref(true)
const showCart = ref(false)
const currentLanguage = ref('es')
const cart = ref([])

// Language translations
const translations = {
  es: {
    menu: {
      nosotros: 'Nosotros',
      productos: 'Productos',
      'producto-estrella': 'Producto Estrella',
      contacto: 'Contacto'
    },
    hero: {
      title: 'Kashmir Cerveza Artesanal',
      subtitle: 'Donde la tradición de Kashmir se encuentra con el arte cervecero. Más de 500 clientes satisfechos y creciendo.',
      cta: {
        products: 'Ver Cervezas',
        order: 'Ordenar Ahora'
      }
    },
    about: {
      title: 'Sobre Nosotros',
      description: 'En Kashmir, creemos en la bondad de la cerveza artesanal y en hacer que las cosas buenas estén disponibles para todos. Nos enorgullece ofrecer una experiencia única que combina la mística de Kashmir con sabores extraordinarios.',
      delivery: {
        title: 'Entrega Rápida',
        description: 'Máximo 2 días en toda la ciudad'
      },
      quality: {
        title: 'Calidad Premium',
        description: 'Ingredientes seleccionados'
      },
      customers: {
        title: '+500 Clientes',
        description: 'Satisfechos y felices'
      }
    },
    starProduct: {
      subtitle: 'Producto Estrella',
      title: 'Cocaina Rusa',
      description: 'Una imperial stout robusta y potente con un 10% de alcohol. Su sabor intenso y complejo te transportará a las frías noches rusas con notas de café, chocolate y un final suave y cálido.',
      cta: 'La quiero'
    },
    products: {
      title: 'Nuestras Cervezas',
      addToCart: 'Agregar al Carrito',
      russianCocaine: {
        name: 'Cocaina Rusa',
        type: 'Imperial Stout - 10% ABV',
        description: 'Una imperial stout robusta y potente. Su sabor intenso y complejo te transportará a las frías noches rusas con notas de café, chocolate y un final suave y cálido.'
      },
      mexicanLove: {
        name: 'Amor a la Mexicana',
        type: 'Mexican Lager - 6.5% ABV',
        description: 'Una cerveza artesanal que rinde homenaje a la rica tradición cervecera mexicana. Con un toque de chile habanero y lima, esta cerveza te transportará a las calles coloridas de México.'
      }
    },
    seasonalProducts: {
      title: 'Cervezas de Temporada',
      subtitle: 'Ediciones Especiales',
      description: 'Descubre nuestras cervezas únicas de temporada, elaboradas con ingredientes especiales y mucho amor.'
    },
    contact: {
      title: 'Contáctanos',
      description: '¿Tienes preguntas sobre nuestros productos o entregas? ¿Quieres hacer un pedido? Contáctanos por WhatsApp y te atenderemos con gusto. Entrega en máximo 2 días.',
      cta: 'Ordenar por WhatsApp'
    },
    footer: {
      copyright: '© 2024 Kashmir Cerveza Artesanal. Todos los derechos reservados.'
    },
    promo: {
      title: '¡Oferta Especial!',
      description: 'Aprovecha nuestras cervezas. Por la compra de 2 o más, obtendrás un 10% de descuento.',
      accept: 'Entendido'
    },
    cart: {
      title: 'Carrito de Compras',
      empty: 'Tu carrito está vacío',
      total: 'Total',
      checkout: 'Ordenar por WhatsApp'
    },
    beers: {
      jengibre: {
        name: 'Jengibre',
        type: 'Amber Ale - 5.4% ABV - IBU: 21',
        description: 'Esta amber combinada con el jengibre crea una sensación cálida que luego da paso a las maltas y te llenará de sensaciones.'
      },
      colonial: {
        name: 'Colonial',
        type: 'IPA - 5.0% ABV - IBU: 43',
        description: 'Una rubia muy aromática y frutal. Amigable con los que quieren adentrarse en el mundo de las IPA.'
      },
      bastarda: {
        name: 'Bastarda',
        type: 'Brown Ale - 5.8% ABV - IBU: 24',
        description: 'Esta infame pero sorprendete y deliciosa cerveza te introducirá al mundo de las cervezas oscuras.'
      },
      palida: {
        name: 'La Pálida',
        type: 'Pale Ale - 5.8% ABV - IBU: 21',
        description: 'Una rubia suave con aroma y sabor a malta, frutal y espaciado y un amargor moderado.'
      },
      mielImperial: {
        name: 'Miel Imperial',
        type: 'Amber Ale - 8.0% ABV - IBU: 19',
        description: 'Su color ámbar evoca la miel artesanal con la que es elaborada. Su sabor y olor te transportará a los verdes del occidente antioqueño.'
      }
    }
  },
  en: {
    menu: {
      nosotros: 'About',
      productos: 'Products',
      'producto-estrella': 'Star Product',
      contacto: 'Contact'
    },
    hero: {
      title: 'Kashmir Craft Beer',
      subtitle: 'Where Kashmir tradition meets the art of brewing. Over 500 satisfied customers and growing.',
      cta: {
        products: 'View Beers',
        order: 'Order Now'
      }
    },
    about: {
      title: 'About Us',
      description: 'At Kashmir, we believe in the goodness of craft beer and making great things available to everyone. We take pride in offering a unique experience that combines the mystique of Kashmir with extraordinary flavors.',
      delivery: {
        title: 'Fast Delivery',
        description: 'Maximum 2 days citywide'
      },
      quality: {
        title: 'Premium Quality',
        description: 'Selected ingredients'
      },
      customers: {
        title: '+500 Customers',
        description: 'Satisfied and happy'
      }
    },
    starProduct: {
      subtitle: 'Star Product',
      title: 'Russian Cocaine',
      description: 'A robust and powerful imperial stout with 10% alcohol. Its intense and complex flavor will transport you to cold Russian nights with notes of coffee, chocolate, and a smooth, warm finish.',
      cta: 'I want it'
    },
    products: {
      title: 'Our Beers',
      addToCart: 'Add to Cart',
      russianCocaine: {
        name: 'Russian Cocaine',
        type: 'Imperial Stout - 10% ABV',
        description: 'A robust and powerful imperial stout. Its intense and complex flavor will transport you to cold Russian nights with notes of coffee, chocolate, and a smooth, warm finish.'
      },
      mexicanLove: {
        name: 'Mexican Love',
        type: 'Mexican Lager - 6.5% ABV',
        description: 'A craft beer that pays homage to the rich Mexican brewing tradition. With a touch of habanero pepper and lime, this beer will transport you to the colorful streets of Mexico.'
      }
    },
    seasonalProducts: {
      title: 'Seasonal Beers',
      subtitle: 'Special Editions',
      description: 'Discover our unique seasonal beers, crafted with special ingredients and lots of love.'
    },
    contact: {
      title: 'Contact Us',
      description: 'Have questions about our products or deliveries? Want to place an order? Contact us via WhatsApp and we\'ll be happy to assist you. Delivery within 2 days max.',
      cta: 'Order via WhatsApp'
    },
    footer: {
      copyright: '© 2024 Kashmir Craft Beer. All rights reserved.'
    },
    promo: {
      title: 'Special Offer!',
      description: 'Take advantage of our beers. Get a 10% discount when you buy 2 or more.',
      accept: 'Got it'
    },
    cart: {
      title: 'Shopping Cart',
      empty: 'Your cart is empty',
      total: 'Total',
      checkout: 'Order via WhatsApp'
    },
    beers: {
      jengibre: {
        name: 'Ginger',
        type: 'Amber Ale - 5.4% ABV - IBU: 21',
        description: 'This amber combined with ginger creates a warm sensation that gives way to malts and will fill you with sensations.'
      },
      colonial: {
        name: 'Colonial',
        type: 'IPA - 5.0% ABV - IBU: 43',
        description: 'A very aromatic and fruity blonde. Friendly for those who want to enter the world of IPAs.'
      },
      bastarda: {
        name: 'Bastard',
        type: 'Brown Ale - 5.8% ABV - IBU: 24',
        description: 'This infamous but surprising and delicious beer will introduce you to the world of dark beers.'
      },
      palida: {
        name: 'The Pale One',
        type: 'Pale Ale - 5.8% ABV - IBU: 21',
        description: 'A smooth blonde with malt aroma and flavor, fruity and spaced with moderate bitterness.'
      },
      mielImperial: {
        name: 'Imperial Honey',
        type: 'Amber Ale - 8.0% ABV - IBU: 19',
        description: 'Its amber color evokes the artisanal honey with which it is made. Its flavor and aroma will transport you to the greens of western Antioquia.'
      }
    }
  }
}

// Beers data
const beers = computed(() => [
  {
    id: 1,
    name: t('beers.jengibre.name'),
    type: t('beers.jengibre.type'),
    description: t('beers.jengibre.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-01-05%20at%208.32.39%20PM%20(5)-LeXp5zakJyYOv0a1NfOkwMWqQtDA7W.jpeg',
    colorClass: 'text-orange-400',
    price: 17000
  },
  {
    id: 2,
    name: t('beers.colonial.name'),
    type: t('beers.colonial.type'),
    description: t('beers.colonial.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-01-05%20at%208.32.39%20PM%20(4)-iprrIDxjN8mpZoYt6S2m15lXaYcQ8A.jpeg',
    colorClass: 'text-purple-400',
    price: 17000
  },
  {
    id: 3,
    name: t('beers.bastarda.name'),
    type: t('beers.bastarda.type'),
    description: t('beers.bastarda.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-01-05%20at%208.32.39%20PM%20(3)-9gtwiqXgern4Jh2jzPJKbw90oWYgHB.jpeg',
    colorClass: 'text-amber-400',
    price: 17000
  },
  {
    id: 4,
    name: t('beers.palida.name'),
    type: t('beers.palida.type'),
    description: t('beers.palida.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-01-05%20at%208.32.38%20PM%20(1)-d2TybrCN1gMBaXzglw56DuumBAfyW9.jpeg',
    colorClass: 'text-orange-400',
    price: 17000
  },
  {
    id: 5,
    name: t('beers.mielImperial.name'),
    type: t('beers.mielImperial.type'),
    description: t('beers.mielImperial.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-01-05%20at%208.32.38%20PM-FdWGA70KTPtl4vB6MVtCvf1vEhXKHO.jpeg',
    colorClass: 'text-yellow-400',
    price: 17000
  }
])

// Add this inside the setup function
const seasonalProducts = computed(() => [
  {
    id: 'russian-cocaine',
    name: t('products.russianCocaine.name'),
    type: t('products.russianCocaine.type'),
    description: t('products.russianCocaine.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-02-01%20at%2011.47.12-Qcg7uWwbOe6WOp3QiMPEeRUqDgvwTo.jpeg',
    price: 17000,
    colorClass: 'text-amber-400'
  },
  {
    id: 'mexican-love',
    name: t('products.mexicanLove.name'),
    type: t('products.mexicanLove.type'),
    description: t('products.mexicanLove.description'),
    image: 'https://hebbkx1anhila5yf.public.blob.vercel-storage.com/WhatsApp%20Image%202025-02-01%20at%2011.47.11-QKP2QTLlxcaF91gWUkUnV5BASKmh5f.jpeg',
    price: 17000,
    colorClass: 'text-red-400'
  }
])

// Computed properties
const cartItemsCount = computed(() => {
  return cart.value.reduce((total, item) => total + item.quantity, 0)
})

const cartTotal = computed(() => {
  return cart.value.reduce((total, item) => total + (item.price * item.quantity), 0)
})

// Methods
const t = (key) => {
  const keys = key.split('.')
  let value = translations[currentLanguage.value]
  for (const k of keys) {
    value = value[k]
  }
  return value
}

const toggleLanguage = () => {
  currentLanguage.value = currentLanguage.value === 'es' ? 'en' : 'es'
}

const formatPrice = (price) => {
  return new Intl.NumberFormat('es-CO', {
    style: 'currency',
    currency: 'COP',
    minimumFractionDigits: 0
  }).format(price)
}

const closeModal = () => {
  showModal.value = false
}

const toggleCart = () => {
  showCart.value = !showCart.value
}

const addToCart = (product) => {
  const existingItem = cart.value.find(item => item.id === product.id)
  if (existingItem) {
    existingItem.quantity++
  } else {
    cart.value.push({
      ...product,
      quantity: 1
    })
  }
  showCart.value = true
}

const updateQuantity = (id, change) => {
  const item = cart.value.find(item => item.id === id)
  if (item) {
    item.quantity = Math.max(1, item.quantity + change)
  }
}

const updateQuantityDirectly = (id, newQuantity) => {
  const item = cart.value.find(item => item.id === id)
  if (item) {
    item.quantity = Math.max(1, newQuantity)
  }
}

const removeFromCart = (id) => {
  cart.value = cart.value.filter(item => item.id !== id)
}

const generateWhatsAppCartLink = () => {
  const discount = cartItemsCount.value >= 2 ? 0.1 : 0
  const subtotal = cartTotal.value
  const total = subtotal * (1 - discount)

  const message = encodeURIComponent(
      `¡Hola! Quiero realizar un pedido con estos productos:\n\n` +
      cart.value.map(item => `- ${item.name} x${item.quantity} (${formatPrice(item.price * item.quantity)})`).join('\n') +
      `\n\nSubtotal: ${formatPrice(subtotal)}` +
      (discount > 0 ? `\nDescuento (10%): ${formatPrice(subtotal * discount)}` : '') +
      `\nTotal: ${formatPrice(total)}`
  )

  return `https://wa.me/573217297970?text=${message}`
}

const handleScroll = () => {
  scrolled.value = window.scrollY > 50
}

// Lifecycle hooks
onMounted(() => {
  window.addEventListener('scroll', handleScroll)
  const savedCart = localStorage.getItem('cart')
  if (savedCart) {
    cart.value = JSON.parse(savedCart)
  }
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

// Watch for changes in the cart and save to localStorage
watch(cart, (newCart) => {
  localStorage.setItem('cart', JSON.stringify(newCart))
}, { deep: true })
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap');

:root {
  --color-bg: #0A2815;
  --color-text: #ffffff;
  --color-primary: #FFA500;
  --color-secondary: #4CAF50;
}

body {
  font-family: 'Poppins', sans-serif;
  background-color: var(--color-bg);
  color: var(--color-text);
}

/* Transitions */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-left-enter-active,
.slide-left-leave-active {
  transition: transform 0.3s ease-out;
}

.slide-left-enter-from,
.slide-left-leave-to {
  transform: translateX(100%);
}

/* Parallax effect */
.parallax {
  background-attachment: fixed;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

/* Animations */
.fade-up-enter-active,
.fade-up-leave-active {
  transition: all 0.5s ease;
}

.fade-up-enter-from,
.fade-up-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

@keyframes float {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

.animate-float {
  animation: float 3s ease-in-out infinite;
}

/* Custom scrollbar */
::-webkitscrollbar {
  width: 10px;
}

::-webkit-scrollbar-track {
  background: var(--color-bg);
}

::-webkit-scrollbar-thumb {
  background: var(--color-primary);
  border-radius: 5px;
}

::-webkit-scrollbar-thumb:hover {
  background: var(--color-secondary);
}

/* Add smooth scrolling */
html {
  scroll-behavior: smooth;
}

/* Add perspective for 3D transforms */
.perspective {
  perspective: 1000px;
}
</style>

