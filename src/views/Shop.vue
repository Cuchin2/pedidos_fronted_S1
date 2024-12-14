<template>
    <div>
        <!-- Mobile Menu Button -->
        <button @click="mobileMenuOpen = !mobileMenuOpen"
            class="md:hidden fixed top-4 left-4 z-50 bg-blue-500 text-white p-2 rounded-lg">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
            </svg>
        </button>

        <!-- Wishlist Button -->
        <div class="fixed top-4 right-20 z-50">
            <button @click="wishlistOpen = !wishlistOpen"
                class="relative bg-white p-3 rounded-full shadow-lg hover:shadow-xl transition-shadow wishlist-button">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-700" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                </svg>
                <span v-if="wishlist.length > 0"
                    class="absolute -top-1 -right-1 bg-red-500 text-white text-xs font-bold rounded-full h-5 w-5 flex items-center justify-center">
                    {{ wishlist.length }}
                </span>
            </button>

            <!-- Wishlist Dropdown -->
            <div v-if="wishlistOpen" class="fixed inset-0 bg-transparent" @click="wishlistOpen = false"></div>
            <div v-if="wishlistOpen" class="absolute right-0 mt-3 w-96 bg-white rounded-lg shadow-xl cart-dropdown">
                <div class="p-4">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-xl font-bold">Wishlist</h2>
                        <button @click="clearWishlist" class="text-red-500 hover:text-red-600 flex items-center gap-1"
                            v-if="wishlist.length > 0">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20"
                                fill="currentColor">
                                <path fill-rule="evenodd"
                                    d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                                    clip-rule="evenodd" />
                            </svg>
                            Clear
                        </button>
                    </div>

                    <div class="max-h-96 overflow-y-auto">
                        <div v-if="wishlist.length === 0" class="text-center text-gray-500 py-8">
                            Your wishlist is empty
                        </div>

                        <div v-for="item in wishlist" :key="item.id"
                            class="flex justify-between items-center border-b pb-2 mb-2">
                            <div class="flex items-center gap-2">
                                <img :src="item.image" :alt="item.name" class="w-16 h-16 object-cover rounded">
                                <div>
                                    <h3 class="font-semibold">{{ item.name }}</h3>
                                    <p class="text-sm text-gray-600">${{ item.price.toFixed(2) }}</p>
                                </div>
                            </div>
                            <div class="flex items-center space-x-2">
                                <button @click="addToCart(item, $event)"
                                    class="px-3 py-1 bg-blue-500 text-white rounded hover:bg-blue-600"
                                    :disabled="getRemainingStock(item) <= 0">
                                    Add to Cart
                                </button>
                                <button @click="removeFromWishlist(item.id)" class="text-red-500 hover:text-red-600">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20"
                                        fill="currentColor">
                                        <path fill-rule="evenodd"
                                            d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                                            clip-rule="evenodd" />
                                    </svg>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Cart Button -->
        <div class="fixed top-4 right-4 z-50">
            <button @click="cartOpen = !cartOpen"
                class="relative bg-white p-3 rounded-full shadow-lg hover:shadow-xl transition-shadow cart-button">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-700" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
                </svg>
                <span v-if="cart.length > 0"
                    class="absolute -top-1 -right-1 bg-red-500 text-white text-xs font-bold rounded-full h-5 w-5 flex items-center justify-center">
                    {{ cart.length }}
                </span>
            </button>

            <!-- Dropdown Cart -->
            <div v-if="cartOpen" class="fixed inset-0 bg-transparent" @click="cartOpen = false"></div>
            <div v-if="cartOpen" class="absolute right-0 mt-3 w-96 bg-white rounded-lg shadow-xl cart-dropdown">

                <div class="p-4">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-xl font-bold">Shopping Cart</h2>
                        <button @click="clearCart" class="text-red-500 hover:text-red-600 flex items-center gap-1"
                            v-if="cart.length > 0">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20"
                                fill="currentColor">
                                <path fill-rule="evenodd"
                                    d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                                    clip-rule="evenodd" />
                            </svg>
                            Clear
                        </button>
                    </div>

                    <div class="max-h-72 overflow-y-auto">
                        <div v-if="cart.length === 0" class="text-center text-gray-500 py-8">
                            Your cart is empty
                        </div>

                        <div v-for="item in cart" :key="item.id"
                            class="flex justify-between items-center border-b pb-2 mb-2">
                            <div class="flex items-center gap-2">
                                <img :src="item.image" :alt="item.name" class="w-16 h-16 object-cover rounded">
                                <div>
                                    <h3 class="font-semibold">{{ item.name }}</h3>
                                    <p class="text-sm text-gray-600">${{ item.price.toFixed(2) }}</p>
                                </div>
                            </div>
                            <div class="flex items-center space-x-2">
                                <input type="number" v-model="item.quantity" @input="updateQuantity(item, $event)"
                                    class="w-16 px-2 py-1 border rounded">
                                <button @click="removeFromCart(item.id)" class="text-red-500 hover:text-red-600">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20"
                                        fill="currentColor">
                                        <path fill-rule="evenodd"
                                            d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                                            clip-rule="evenodd" />
                                    </svg>
                                </button>
                            </div>
                        </div>
                    </div>

                    <div class="mt-4 pt-4 border-t" v-if="cart.length > 0">
                        <div class="flex justify-between items-center font-bold">
                            <span>Total:</span>
                            <span>${{ total.toFixed(2) }}</span>
                        </div>
                        <button @click="openWhatsApp"
                            class="mt-4 w-full bg-green-500 text-white px-4 py-2 rounded-lg hover:bg-green-600 transition-colors">
                            Checkout via WhatsApp
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Sidebar -->
        <div :class="{ 'translate-x-0': mobileMenuOpen, '-translate-x-full': !mobileMenuOpen }"
            class="fixed md:translate-x-0 left-0 top-0 h-full w-64 bg-white shadow-lg transition-transform duration-300 ease-in-out z-40">
            <div class="p-4">
                <h1 class="text-2xl font-bold mb-8 text-gray-800">Order System</h1>

                <!-- Filters -->
                <div class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">Search</label>
                        <input v-model="searchQuery" type="text" placeholder="Search products..."
                            class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">Category</label>
                        <select v-model="selectedCategory"
                            class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                            <option v-for="category in categories" :key="category" :value="category">
                                {{ category }}
                            </option>
                        </select>
                    </div>

                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-3">Price Range</label>
                        <div class="flex items-center gap-4 mb-4">
                            <span class="text-sm font-medium text-gray-600">$</span>
                            <input type="number" v-model="minPrice"
                                class="w-20 px-2 py-1 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500"
                                min="0" :max="maxProductPrice" @input="validatePriceRange">
                            <span class="text-sm font-medium text-gray-600">to $</span>
                            <input type="number" v-model="maxPrice"
                                class="w-20 px-2 py-1 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500"
                                min="0" :max="maxProductPrice" @input="validatePriceRange">
                        </div>
                        <div class="range-slider">
                            <div class="track"></div>
                            <div class="range" :style="{
                                left: ((minPrice / maxProductPrice) * (100 - (28 / windowWidth * 100))) + '%',
                                right: (100 - (maxPrice / maxProductPrice) * (100 - (28 / windowWidth * 100))) + '%'
                            }"></div>
                            <input type="range" v-model="minPrice" min="0" :max="maxProductPrice" step="0.01"
                                @input="validatePriceRange">
                            <input type="range" v-model="maxPrice" min="0" :max="maxProductPrice" step="0.01"
                                @input="validatePriceRange">
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Main Content -->
        <div class="md:ml-64 p-4">
            <div class="container mx-auto">
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                    <div v-for="product in filteredProducts" :key="product.id"
                        class="bg-white p-4 rounded-lg shadow hover:shadow-lg transition-shadow">
                        <div class="product-image-container cursor-pointer" @click="openProductModal(product)">
                            <img :src="product.image" :alt="product.name">
                        </div>
                        <h3 class="text-lg font-semibold">{{ product.name }}</h3>
                        <p class="text-gray-600">{{ product.category }}</p>
                        <p class="text-blue-600 font-bold">${{ product.price.toFixed(2) }}</p>
                        <p class="text-sm text-gray-500">Stock: {{ getRemainingStock(product) }}</p>
                        <button @click="toggleWishlist(product, $event)"
                            class="mt-2 w-full px-4 py-2 rounded-lg transition-colors mb-2"
                            :class="isInWishlist(product.id) ? 'bg-red-500 hover:bg-red-600 text-white' : 'bg-gray-200 hover:bg-gray-300'">
                            {{ isInWishlist(product.id) ? 'Remove from Wishlist' : 'Add to Wishlist' }}
                        </button>
                        <button @click="addToCart(product, $event)" :disabled="getRemainingStock(product) <= 0" :class="[
                            'mt-2 w-full px-4 py-2 rounded-lg transition-colors',
                            getRemainingStock(product) <= 0
                                ? 'bg-gray-400 cursor-not-allowed'
                                : 'bg-blue-500 hover:bg-blue-600 text-white'
                        ]">
                            {{ getRemainingStock(product) <= 0 ? 'Out of Stock' : 'Add to Cart' }} </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Product Modal -->
        <div v-if="showModal" class="fixed inset-0 z-50 overflow-y-auto" role="dialog">
            <!-- Backdrop -->
            <div class="fixed inset-0 bg-black bg-opacity-50 transition-opacity"></div>

            <!-- Modal -->
            <div class="relative min-h-screen flex items-center justify-center p-4">
                <div v-click-away="closeModal"
                    class="relative bg-white rounded-lg max-w-2xl w-full overflow-hidden shadow-xl transform transition-all">
                    <!-- Close button -->
                    <button @click="closeModal" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                            stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M6 18L18 6M6 6l12 12" />
                        </svg>
                    </button>

                    <!-- Content -->
                    <div v-if="selectedProduct" class="p-6">
                        <div class="flex flex-col md:flex-row gap-6">
                            <!-- Product Image -->
                            <div class="w-full md:w-1/2">
                                <img :src="selectedProduct.image" :alt="selectedProduct.name"
                                    class="w-full h-64 object-cover rounded-lg">
                            </div>

                            <!-- Product Details -->
                            <div class="w-full md:w-1/2">
                                <h2 class="text-2xl font-bold mb-2">{{ selectedProduct.name }}</h2>
                                <p class="text-gray-600 mb-4">{{ selectedProduct.category }}</p>
                                <p class="text-3xl font-bold text-blue-600 mb-4">${{ selectedProduct.price.toFixed(2) }}
                                </p>
                                <p class="text-gray-600 mb-4">Stock: {{ getRemainingStock(selectedProduct) }}</p>

                                <!-- Actions -->
                                <div class="space-y-3">
                                    <button @click="toggleWishlist(selectedProduct, $event)"
                                        class="w-full px-4 py-2 rounded-lg transition-colors"
                                        :class="isInWishlist(selectedProduct.id) ? 'bg-red-500 hover:bg-red-600 text-white' : 'bg-gray-200 hover:bg-gray-300'">
                                        {{ isInWishlist(selectedProduct.id) ? 'Remove from Wishlist' : 'Add to Wishlist'
                                        }}
                                    </button>

                                    <button @click="addToCart(selectedProduct, $event)"
                                        :disabled="getRemainingStock(selectedProduct) <= 0" :class="[
                                            'w-full px-4 py-2 rounded-lg transition-colors',
                                            getRemainingStock(selectedProduct) <= 0
                                                ? 'bg-gray-400 cursor-not-allowed'
                                                : 'bg-blue-500 hover:bg-blue-600 text-white'
                                        ]">
                                        {{ getRemainingStock(selectedProduct) <= 0 ? 'Out of Stock' : 'Add to Cart' }}
                                            </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { Cart } from '@/interfaces/cart'
import { Product } from '@/interfaces/product'
const windowWidth = ref(window.innerWidth)

// Add window resize handler
window.addEventListener('resize', () => {
    windowWidth.value = window.innerWidth
})

const mobileMenuOpen = ref(false)
const cartOpen = ref(false) // Added cartOpen reference
const wishlist = ref<Product[]>([])
const wishlistOpen = ref<boolean>(false)

const selectedProduct = ref<Product | null>(null)
const showModal = ref(false)

const openProductModal = (product: Product) => {
    selectedProduct.value = product
    showModal.value = true
}

const closeModal = () => {
    showModal.value = false
    selectedProduct.value = null
}

const products = ref([
    {
        id: 1,
        name: 'Pizza Margherita',
        price: 12.99,
        category: 'Pizza',
        stock: 20,
        image: 'https://images.unsplash.com/photo-1604382355076-af4b0eb60143?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=687&q=80'
    },
    {
        id: 2,
        name: 'Hamburger Classic',
        price: 8.99,
        category: 'Burgers',
        stock: 15,
        image: 'https://images.unsplash.com/photo-1568901346375-23c9450c58cd?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1899&q=80'
    },
    {
        id: 3,
        name: 'Caesar Salad',
        price: 7.99,
        category: 'Salads',
        stock: 10,
        image: 'https://images.unsplash.com/photo-1546793665-c74683f339c1?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=687&q=80'
    },
    {
        id: 4,
        name: 'Spaghetti Carbonara',
        price: 11.99,
        category: 'Pasta',
        stock: 25,
        image: 'https://images.unsplash.com/photo-1612874742237-6526221588e3?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=711&q=80'
    },
    {
        id: 5,
        name: 'Pepperoni Pizza',
        price: 14.99,
        category: 'Pizza',
        stock: 18,
        image: 'https://images.unsplash.com/photo-1628840042765-356cda07504e?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=880&q=80'
    },
    {
        id: 6,
        name: 'Chicken Wings',
        price: 9.99,
        category: 'Appetizers',
        stock: 30,
        image: 'https://images.unsplash.com/photo-1567620832903-9fc6debc209f?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=780&q=80'
    },
    {
        id: 7,
        name: 'Veggie Burger',
        price: 10.99,
        category: 'Burgers',
        stock: 12,
        image: 'https://images.unsplash.com/photo-1520072959219-c595dc870360?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=690&q=80'
    },
    {
        id: 8,
        name: 'Greek Salad',
        price: 8.99,
        category: 'Salads',
        stock: 15,
        image: 'https://images.unsplash.com/photo-1540420773420-3366772f4999?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=684&q=80'
    },
    {
        id: 9,
        name: 'Lasagna',
        price: 13.99,
        category: 'Pasta',
        stock: 20,
        image: 'https://images.unsplash.com/photo-1574894709920-11b28e7367e3?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=735&q=80'
    },
    {
        id: 10,
        name: 'Mozzarella Sticks',
        price: 6.99,
        category: 'Appetizers',
        stock: 25,
        image: 'https://images.unsplash.com/photo-1531749668029-2db88e4276c7?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=687&q=80'
    },
    {
        id: 11,
        name: 'BBQ Ribs',
        price: 18.99,
        category: 'Main Dishes',
        stock: 15,
        image: 'https://images.unsplash.com/photo-1544025162-d76694265947?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=869&q=80'
    },
    {
        id: 12,
        name: 'Chocolate Cake',
        price: 5.99,
        category: 'Desserts',
        stock: 20,
        image: 'https://images.unsplash.com/photo-1578985545062-69928b1d9587?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=789&q=80'
    }
])

const cart = ref<Cart[]>([])
const searchQuery = ref('')
const selectedCategory = ref('all')
const minPrice = ref<any>(0)
const maxProductPrice = computed(() => {
    return Math.max(...products.value.map(p => p.price))
})
const maxPrice = ref<any>(maxProductPrice.value)

const categories = computed(() => {
    const cats = new Set(products.value.map(p => p.category))
    return ['all', ...Array.from(cats)]
})

const filteredProducts = computed(() => {
    return products.value.filter(product => {
        const matchesSearch = product.name.toLowerCase().includes(searchQuery.value.toLowerCase())
        const matchesCategory = selectedCategory.value === 'all' || product.category === selectedCategory.value
        const matchesPrice = product.price >= minPrice.value && product.price <= maxPrice.value
        return matchesSearch && matchesCategory && matchesPrice
    })
})

const total = computed(() => {
    return cart.value.reduce((sum, item) => sum + (item.price * item.quantity), 0)
})

const getRemainingStock = (product: Product) => {
    const cartItem = cart.value.find(item => item.id === product.id)
    return product.stock - (cartItem?.quantity || 0)
}

const createFloatingImage = (sourceImg: string, event: MouseEvent) => {
    const floatingImg = document.createElement('img')
    floatingImg.src = sourceImg
    floatingImg.classList.add('floating-image')

    const sourceRect = (event.target as HTMLElement).getBoundingClientRect();
    // Busca el botón del carrito
    const cartButton = document.querySelector('.cart-button') as HTMLElement | null;
    const targetRect = cartButton?.getBoundingClientRect();

    floatingImg.style.width = '100px'
    floatingImg.style.height = '100px'
    floatingImg.style.objectFit = 'cover'
    floatingImg.style.borderRadius = '8px'
    floatingImg.style.left = `${sourceRect.left}px`
    floatingImg.style.top = `${sourceRect.top}px`

    document.body.appendChild(floatingImg)

    // Comprueba que targetRect no sea undefined antes de usarlo
    if (targetRect) {
        requestAnimationFrame(() => {
            floatingImg.style.transform = `translate(${targetRect.left - sourceRect.left}px, ${targetRect.top - sourceRect.top}px) scale(0.3)`;
            floatingImg.classList.add('fade-out');
        });
    }

    // Increase cleanup timeout to match new animation duration
    setTimeout(() => {
        floatingImg.remove()
    }, 1000) // Keep this at 1000ms to ensure animation completes
}
const createFloatingWishlistImage = (sourceImg: string, event: MouseEvent) => {
    const floatingImg = document.createElement('img')
    floatingImg.src = sourceImg
    floatingImg.classList.add('floating-image')

    const sourceRect = (event.target as HTMLElement).getBoundingClientRect();
    const wishlistButton = document.querySelector('.wishlist-button') as HTMLElement | null;
    const targetRect = wishlistButton?.getBoundingClientRect()

    floatingImg.style.width = '100px'
    floatingImg.style.height = '100px'
    floatingImg.style.objectFit = 'cover'
    floatingImg.style.borderRadius = '8px'
    floatingImg.style.left = `${sourceRect.left}px`
    floatingImg.style.top = `${sourceRect.top}px`

    document.body.appendChild(floatingImg)
    if (targetRect) {
        requestAnimationFrame(() => {
            floatingImg.style.transform = `translate(${targetRect.left - sourceRect.left}px, ${targetRect.top - sourceRect.top}px) scale(0.3)`
            floatingImg.classList.add('fade-out')
        })
    }
    setTimeout(() => {
        floatingImg.remove()
    }, 1000)
}
const addToCart = (product: Product, event: MouseEvent) => {
    const existingItem = cart.value.find(item => item.id === product.id)
    const remainingStock = getRemainingStock(product)

    if (remainingStock <= 0) return

    // Create floating animation
    createFloatingImage(product.image, event)

    if (existingItem) {
        if (existingItem.quantity < product.stock) {
            existingItem.quantity++
        }
    } else {
        cart.value.push({ ...product, quantity: 1 })
    }

    // Remove from wishlist if the item was there
    const wishlistIndex = wishlist.value.findIndex(item => item.id === product.id)
    if (wishlistIndex !== -1) {
        wishlist.value.splice(wishlistIndex, 1)
    }
}

const removeFromCart = (productId: number) => {
    cart.value = cart.value.filter(item => item.id !== productId);
}

const clearCart = () => {
    cart.value = []
    cartOpen.value=false
}

const updateQuantity = (item: Cart, event: Event) => {
    if (event.target instanceof HTMLInputElement) {
        const newQuantity = event.target.value;
        const quantity = parseInt(newQuantity);
        const product = products.value.find(p => p.id === item.id);

        if (quantity <= 0) {
            removeFromCart(item.id);
        } else if (product && quantity <= product.stock) {
            item.quantity = quantity;
        } else if (product && quantity > product.stock) {
            item.quantity = product.stock;
        }
    }
};

const validatePriceRange = () => {
    const min = parseFloat(minPrice.value); // Convierte a número para operar
    const max = parseFloat(maxPrice.value);
    const maxAllowed = maxProductPrice.value; // Suponiendo que ya es número

    // Validación: si el valor mínimo es mayor que el máximo
    if (min > max) {
        minPrice.value = max.toString(); // Convierte el número a string antes de asignar
    }

    // Validación: si el valor máximo es menor que el mínimo
    if (max < min) {
        maxPrice.value = min.toString(); // Convierte el número a string antes de asignar
    }

    // Ajuste de los rangos con límites y conversión final a string
    minPrice.value = Math.max(0, Math.min(min, maxAllowed)).toString(); // Convierte el número a string
    maxPrice.value = Math.max(0, Math.min(max, maxAllowed)).toString(); // Convierte el número a string
};


const isInWishlist = (productId: number) => {
    return wishlist.value.some(item => item.id === productId)
}

const toggleWishlist = (product: Product, event: MouseEvent) => {
    const index = wishlist.value.findIndex(item => item.id === product.id)
    if (index === -1) {
        wishlist.value.push({ ...product })
        createFloatingWishlistImage(product.image, event)
    } else {
        wishlist.value.splice(index, 1)
    }
}

const removeFromWishlist = (productId: number) => {
    wishlist.value = wishlist.value.filter(item => item.id !== productId)
}

const clearWishlist = () => {
    wishlist.value = []
    wishlistOpen.value=false

}

const openWhatsApp = () => {
    // Format cart items into a message
    const message = cart.value.reduce((msg, item) => {
        return msg + `\n• ${item.quantity}x ${item.name} - $${(item.price * item.quantity).toFixed(2)}`
    }, `Hello! I would like to place an order for:\n`)

    // Add total
    const orderMessage = message + `\n\nTotal: $${total.value.toFixed(2)}`

    // Format the message for URL
    const encodedMessage = encodeURIComponent(orderMessage)

    // WhatsApp number
    const phoneNumber = '51930915760'

    // Open WhatsApp Web
    window.open(`https://wa.me/${phoneNumber}?text=${encodedMessage}`, '_blank')
}


</script>

<style>
input[type="range"] {
    -webkit-appearance: none;
    width: 100%;
    height: 4px;
    background: transparent;
    position: absolute;
    left: 0;
    top: 50%;
    transform: translateY(-50%);
    pointer-events: none;
    z-index: 4;
}

input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 24px;
    height: 24px;
    background: linear-gradient(145deg, #6366f1, #4f46e5);
    border: 2px solid white;
    border-radius: 50%;
    box-shadow: 0 2px 8px rgba(99, 102, 241, 0.3);
    cursor: pointer;
    pointer-events: all;
    position: relative;
    z-index: 5;
    transition: all 0.2s ease;
}

input[type="range"]::-webkit-slider-thumb:hover {
    transform: scale(1.1);
    box-shadow: 0 4px 12px rgba(99, 102, 241, 0.4);
}

input[type="range"]::-moz-range-thumb {
    width: 24px;
    height: 24px;
    background: linear-gradient(145deg, #6366f1, #4f46e5);
    border: 2px solid white;
    border-radius: 50%;
    box-shadow: 0 2px 8px rgba(99, 102, 241, 0.3);
    cursor: pointer;
    pointer-events: all;
    position: relative;
    z-index: 5;
    transition: all 0.2s ease;
}

input[type="range"]::-moz-range-thumb:hover {
    transform: scale(1.1);
    box-shadow: 0 4px 12px rgba(99, 102, 241, 0.4);
}

.range-slider {
    position: relative;
    height: 48px;
    padding: 0 12px;
    background: #ffffff;
    border-radius: 12px;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.06);
    margin: 16px 0;
    transition: all 0.2s ease;
}

.range-slider:hover {
    box-shadow: 0 6px 24px rgba(0, 0, 0, 0.08);
}

.range-slider .track,
.range-slider .range {
    position: absolute;
    left: 12px;
    right: 12px;
    top: 50%;
    transform: translateY(-50%);
    height: 4px;
    border-radius: 4px;
}

.range-slider .track {
    background: #e5e7eb;
    z-index: 1;
}

.range-slider .range {
    background: linear-gradient(90deg, #6366f1, #4f46e5);
    z-index: 2;
}

/* Price input styling */
input[type="number"] {
    width: 64px;
    font-size: 0.875rem;
    font-weight: 500;
    color: #4b5563;
    border: 1px solid #e5e7eb;
    border-radius: 8px;
    padding: 6px 8px;
    background: #ffffff;
    transition: all 0.2s ease;
}

input[type="number"]:hover {
    border-color: #6366f1;
}

input[type="number"]:focus {
    border-color: #4f46e5;
    box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2);
    outline: none;
}

.range-slider+div span.text-sm {
    font-size: 0.813rem;
    color: #6b7280;
}

.floating-image {
    position: fixed;
    pointer-events: none;
    z-index: 9999;
    transition: all 0.8s cubic-bezier(0.2, 1, 0.3, 1);
    opacity: 1;
}

.floating-image.fade-out {
    opacity: 0;
    transform: scale(0.5);
}

/* Add these styles to handle the dropdown */
.cart-dropdown {
    transform-origin: top right;
    animation: dropdown 0.2s ease-out;
}

@keyframes dropdown {
    from {
        opacity: 0;
        transform: scale(0.95);
    }

    to {
        opacity: 1;
        transform: scale(1);
    }
}

/* New CSS for product image container */
.product-image-container {
    overflow: hidden;
    border-radius: 0.5rem;
    margin-bottom: 1rem;
}

.product-image-container img {
    width: 100%;
    height: 12rem;
    object-fit: cover;
    transition: transform 0.3s ease;
}

.product-image-container:hover img {
    transform: scale(1.1);
}
</style>