<template>
  <div class="container mt-4">
    <h2 class="text-center mb-4">Productos NFL</h2>

    <ProductSearch 
      :products="products" 
      @search-results="updateDisplayedProducts" 
    />

    <div class="row">
      <div 
        v-for="product in displayedProducts" 
        :key="product.id" 
        class="col-lg-4 col-md-6 mb-4"
      >
        <div class="card product-card h-100" @click="openModal(product)">
          <div class="product-image-container">
            <img 
              :src="getImageUrl(product.productimage)" 
              :alt="product.productname"
              class="card-img-top product-image"
            >
          </div>
          
          <div class="card-body d-flex flex-column">
            <h5 class="card-title product-name">{{ product.productname }}</h5>
            <p class="product-price mt-auto">${{ formatPrice(product.productprice) }}</p>
          </div>
        </div>
      </div>
    </div>

    <div v-if="displayedProducts.length === 0 && products.length > 0" class="text-center mt-5">
      <div class="no-products-message">
        <i class="bi bi-search display-1 text-muted mb-3"></i>
        <h4 class="text-muted">No se encontraron productos</h4>
        <p class="text-muted">Intenta con otros términos de búsqueda</p>
      </div>
    </div>

    <div v-if="products.length === 0" class="text-center mt-5">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Cargando productos...</span>
      </div>
      <p class="mt-2 text-muted">Cargando productos...</p>
    </div>

    <ProductModal 
      :show="showModal" 
      :product="selectedProduct"
      @close="closeModal"
      @add-to-cart="handleAddToCart"
    />
  </div>
</template>

<script>
import ProductModal from './ProductModal.vue'
import ProductSearch from './ProductSearch.vue'

export default {
  name: 'ProductList',
  components: {
    ProductModal,
    ProductSearch
  },
  data() {
    return {
      products: [],
      displayedProducts: [],
      selectedProduct: null,
      showModal: false
    }
  },
  async mounted() {
    await this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await fetch('http://localhost:4000/Products');
        this.products = await response.json();
        this.displayedProducts = this.products;
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    },
    
    getImageUrl(imagePath) {
      return imagePath.startsWith('/') ? imagePath : `/${imagePath}`;
    },
    
    formatPrice(price) {
      return new Intl.NumberFormat('es-CL').format(price);
    },
    
    openModal(product) {
      this.selectedProduct = product;
      this.showModal = true;
    },
    
    closeModal() {
      this.showModal = false;
      this.selectedProduct = null;
    },
    
    handleAddToCart(cartItem) {
      this.$emit('add-to-cart', cartItem)
  alert(`¡${cartItem.quantity} ${cartItem.product.productname} agregado(s) al carrito!`)
      
      this.$notify({
        title: 'Producto agregado',
        text: `¡${cartItem.quantity} ${cartItem.product.productname} agregado(s) al carrito!`,
        type: 'success'
      });
      
    },
    updateDisplayedProducts(filteredProducts) {
      this.displayedProducts = filteredProducts;
    }
  }
}
</script>

<style scoped>
.product-card {
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  border: none;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  border-radius: 10px;
  overflow: hidden;
}

.product-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
}

.product-image-container {
  height: 420px;
  overflow: hidden;
}

.product-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.product-card:hover .product-image {
  transform: scale(1.05);
}

.product-name {
  font-size: 1.1rem;
  font-weight: 600;
  color: #333;
  margin-bottom: 0.5rem;
  line-height: 1.4;
}

.product-price {
  font-size: 1.25rem;
  font-weight: 700;
  color: #013369;
  margin-bottom: 0;
}

.card-body {
  padding: 1.25rem;
}

.no-products-message {
  padding: 3rem 1rem;
}

@media (max-width: 768px) {
  .product-image-container {
    height: 200px;
  }
  
  .product-name {
    font-size: 1rem;
  }
  
  .product-price {
    font-size: 1.1rem;
  }
}
</style>