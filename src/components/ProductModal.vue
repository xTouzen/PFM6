<template>
  <div 
    v-if="show" 
    class="modal-custom" 
    tabindex="-1"
    @click="closeModalOnOverlay"
  >
    <div 
      class="modal-dialog-custom"
      @click.stop
    >
      <div class="modal-content-custom">
        <div class="modal-header-custom">
          <h5 class="modal-title">{{ product.productname }}</h5>
          <button type="button" class="btn-close-custom" @click="closeModal">
            <i class="bi bi-x-lg"></i>
          </button>
        </div>
        <div class="modal-body-custom">
          <div class="row">
            <div class="col-md-6">
              <img 
                :src="getImageUrl(product.productimage)" 
                :alt="product.productname"
                class="img-fluid rounded"
              >
            </div>
            
            <div class="col-md-6">
              <p class="fs-4 text-primary fw-bold">${{ formatPrice(product.productprice) }}</p>
              <p><strong>Stock disponible:</strong> {{ product.productstock }} unidades</p>
              
              <div class="mb-4">
                <label for="quantity" class="form-label fw-semibold">Cantidad:</label>
                <div class="input-group" style="max-width: 150px;">
                  <button 
                    class="btn btn-outline-secondary" 
                    type="button" 
                    @click="decreaseQuantity"
                    :disabled="quantity <= 1"
                  >
                    <i class="bi bi-dash"></i>
                  </button>
                  <input 
                    type="number" 
                    class="form-control text-center" 
                    id="quantity" 
                    v-model.number="quantity"
                    min="1"
                    :max="product.productstock"
                    @change="validateQuantity"
                  >
                  <button 
                    class="btn btn-outline-secondary" 
                    type="button" 
                    @click="increaseQuantity"
                    :disabled="quantity >= product.productstock"
                  >
                    <i class="bi bi-plus"></i>
                  </button>
                </div>
                <small class="text-muted">Máximo: {{ product.productstock }} unidades</small>
              </div>
              
              <button 
                class="btn btn-primary btn-lg w-100 py-3"
                @click="addToCart"
                :disabled="quantity > product.productstock"
              >
                <i class="bi bi-cart-plus me-2"></i>Agregar al carrito
              </button>
              
              <div v-if="product.productstock < 10" class="alert alert-warning mt-3">
                <i class="bi bi-exclamation-triangle me-2"></i>
                ¡Quedan pocas unidades!
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductModal',
  props: {
    show: Boolean,
    product: Object
  },
  data() {
    return {
      quantity: 1
    }
  },
  watch: {
    show(newVal) {
      if (newVal) {
        this.quantity = 1;
        document.body.style.overflow = 'hidden';
        document.addEventListener('keydown', this.handleKeydown);
      } else {
        document.body.style.overflow = 'auto';
        document.removeEventListener('keydown', this.handleKeydown);
      }
    }
  },
  methods: {
    getImageUrl(imagePath) {
    return imagePath.startsWith('/') ? imagePath : `/${imagePath}`;
    },
    
    formatPrice(price) {
      return new Intl.NumberFormat('es-CL').format(price);
    },
    
    closeModal() {
      this.$emit('close');
    },
    
    closeModalOnOverlay(event) {
      if (event.target.classList.contains('modal-custom')) {
        this.closeModal();
      }
    },
    
    handleKeydown(event) {
      if (event.key === 'Escape') {
        this.closeModal();
      }
    },
    
    increaseQuantity() {
      if (this.quantity < this.product.productstock) {
        this.quantity++;
      }
    },
    
    decreaseQuantity() {
      if (this.quantity > 1) {
        this.quantity--;
      }
    },
    
    validateQuantity() {
      if (this.quantity < 1) {
        this.quantity = 1;
      } else if (this.quantity > this.product.productstock) {
        this.quantity = this.product.productstock;
      }
    },
    
    addToCart() {
      this.$emit('add-to-cart', {
        product: this.product,
        quantity: this.quantity
      });
      this.closeModal();
    }
  },
  beforeUnmount() {
    document.removeEventListener('keydown', this.handleKeydown);
    document.body.style.overflow = 'auto';
  }
}
</script>

<style scoped>
.modal-custom {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1050;
  background-color: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(3px);
  cursor: pointer; 
}

.modal-dialog-custom {
  max-width: 800px;
  width: 90%;
  margin: 0 auto;
  cursor: default; 
}

.modal-content-custom {
  border: none;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  overflow: hidden;
  animation: modalAppear 0.3s ease-out;
}

@keyframes modalAppear {
  from {
    opacity: 0;
    transform: scale(0.9) translateY(-20px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

.modal-header-custom {
  border-bottom: 1px solid #eee;
  background-color: #f8f9fa;
  padding: 1.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-title {
  font-weight: 700;
  color: #013369;
  margin: 0;
}

.btn-close-custom {
  border: none;
  background: transparent;
  font-size: 1.5rem;
  color: #6c757d;
  cursor: pointer;
  padding: 0.5rem;
  transition: color 0.3s ease;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn-close-custom:hover {
  background-color: rgba(0,0,0,0.1);
  color: #D50A0A;
}

.modal-body-custom {
  padding: 1.5rem;
}

.btn-primary {
  background: linear-gradient(135deg, #013369, #0155a3);
  border: none;
  font-weight: 600;
  transition: all 0.3s ease;
}

.btn-primary:hover:not(:disabled) {
  background: linear-gradient(135deg, #D50A0A, #ff2a2a);
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(213, 10, 10, 0.3);
}

.btn-primary:disabled {
  background: #6c757d;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}

.input-group button {
  width: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.input-group input {
  border-left: none;
  border-right: none;
}

.img-fluid {
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  transition: transform 0.3s ease;
}

.img-fluid:hover {
  transform: scale(1.02);
}

@media (max-width: 768px) {
  .modal-dialog-custom {
    width: 95%;
    margin: 1rem auto;
  }
  
  .modal-custom {
    align-items: flex-start;
    padding-top: 2rem;
  }
  
  .modal-content-custom {
    margin: 1rem;
  }
}
</style>