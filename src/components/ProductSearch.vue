<template>
  <div class="product-search-container">
    <div class="row justify-content-center">
      <div class="col-md-8 col-lg-6">
        <div class="search-box">
          <div class="input-group">
            <span class="input-group-text search-icon">
              <i class="bi bi-search"></i>
            </span>
            <input
              type="text"
              class="form-control search-input"
              placeholder="Buscar productos por nombre..."
              v-model="searchTerm"
              @input="handleSearch"
            />
            <button 
              v-if="searchTerm" 
              class="btn btn-outline-secondary clear-btn" 
              type="button"
              @click="clearSearch"
            >
              <i class="bi bi-x-lg"></i>
            </button>
          </div>
        </div>
        
        <div v-if="searchTerm" class="search-results-info mt-2">
          <small class="text-muted">
            {{ filteredProducts.length }} producto(s) encontrado(s) para "{{ searchTerm }}"
          </small>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductSearch',
  props: {
    products: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      searchTerm: ''
    }
  },
  computed: {
    filteredProducts() {
      if (!this.searchTerm.trim()) {
        return this.products
      }
      
      const term = this.searchTerm.toLowerCase().trim()
      return this.products.filter(product => 
        product.productname.toLowerCase().includes(term)
      )
    }
  },
  methods: {
    handleSearch() {
      this.$emit('search-results', this.filteredProducts)
    },
    
    clearSearch() {
      this.searchTerm = ''
      this.handleSearch()
    }
  },
  watch: {
    products: {
      handler() {
        this.handleSearch()
      },
      immediate: true
    }
  }
}
</script>

<style scoped>
.product-search-container {
  margin-bottom: 2rem;
}

.search-box {
  position: relative;
}

.search-input {
  border: 2px solid #e9ecef;
  border-radius: 25px;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  transition: all 0.3s ease;
  padding-left: 3rem;
}

.search-input:focus {
  border-color: #013369;
  box-shadow: 0 0 0 0.2rem rgba(1, 51, 105, 0.25);
}

.search-icon {
  background: transparent;
  border: none;
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  z-index: 10;
  color: #6c757d;
}

.input-group {
  position: relative;
}

.input-group-text {
  border: none;
  background: transparent;
}

.clear-btn {
  border: none;
  background: transparent;
  color: #6c757d;
  position: absolute;
  right: 0.5rem;
  top: 50%;
  transform: translateY(-50%);
  z-index: 10;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.clear-btn:hover {
  background-color: #f8f9fa;
  color: #D50A0A;
}

.search-results-info {
  text-align: center;
}

.search-input {
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 768px) {
  .search-input {
    font-size: 0.9rem;
    padding: 0.65rem 0.9rem;
    padding-left: 2.5rem;
  }
  
  .clear-btn {
    right: 0.3rem;
  }
}
</style>