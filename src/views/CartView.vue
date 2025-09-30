<template>
  <div class="cart-view">
    <div class="container mt-4">
      <div class="row">
        <div class="col-12">
          <h2 class="text-center mb-4">Carrito de Compras</h2>
          
          <div v-if="cartItems.length === 0" class="empty-cart text-center">
            <i class="bi bi-cart-x display-1 text-muted"></i>
            <h3 class="mt-3 text-muted">Tu carrito está vacío</h3>
            <p class="text-muted">Agrega algunos productos para continuar</p>
            <router-link to="/" class="btn btn-primary mt-3">
              <i class="bi bi-arrow-left me-2"></i>Seguir Comprando
            </router-link>
          </div>

          <div v-else class="row">
            <div class="col-lg-8">
              <ProductCart 
                v-for="item in cartItems" 
                :key="item.product.id"
                :item="item"
                @update-quantity="updateItemQuantity"
                @remove-item="removeItem"
              />
            </div>

            <div class="col-lg-4">
              <div class="cart-summary card">
                <div class="card-body">
                  <h5 class="card-title">Resumen de Compra</h5>
                  
                  <div class="summary-details">
                    <div class="d-flex justify-content-between mb-2">
                      <span>Subtotal:</span>
                      <span>${{ formatPrice(subtotal) }}</span>
                    </div>
                    <div class="d-flex justify-content-between mb-3">
                      <span>Envío:</span>
                      <span class="text-success">Gratis</span>
                    </div>
                    <hr>
                    <div class="d-flex justify-content-between mb-3 total">
                      <strong>Total:</strong>
                      <strong>${{ formatPrice(subtotal) }}</strong>
                    </div>
                  </div>

                  <div class="cart-actions">
                    <button 
                      class="btn btn-outline-danger w-100 mb-2"
                      @click="clearCart"
                    >
                      <i class="bi bi-trash me-2"></i>Limpiar Carrito
                    </button>
                    <button 
                      class="btn btn-success w-100"
                      @click="checkout"
                    >
                      <i class="bi bi-bag-check me-2"></i>Comprar
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="showSuccessModal" class="modal-custom" tabindex="-1" @click="closeSuccessModal">
      <div class="modal-dialog-custom" @click.stop>
        <div class="modal-content-custom">
          <div class="modal-body-custom text-center">
            <i class="bi bi-check-circle-fall text-success display-1"></i>
            <h3 class="mt-3">¡Compra Exitosa!</h3>
            <p class="text-muted">Tu pedido ha sido procesado correctamente.</p>
            <button class="btn btn-primary mt-3" @click="closeSuccessModal">
              Continuar Comprando
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ProductCart from '@/components/ProductCart.vue'

export default {
  name: 'CartView',
  components: {
    ProductCart
  },
  props: {
    cartItems: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      showSuccessModal: false
    }
  },
  computed: {
    subtotal() {
      return this.cartItems.reduce((total, item) => {
        return total + (item.product.productprice * item.quantity)
      }, 0)
    }
  },
  methods: {
    formatPrice(price) {
      return new Intl.NumberFormat('es-CL').format(price)
    },
    
    updateItemQuantity(productId, newQuantity) {
      const updatedCart = this.cartItems.map(item => {
        if (item.product.id === productId) {
          return { ...item, quantity: newQuantity }
        }
        return item
      }).filter(item => item.quantity > 0) 
      
      this.$emit('update-cart', updatedCart)
    },
    
    removeItem(productId) {
      const updatedCart = this.cartItems.filter(item => item.product.id !== productId)
      this.$emit('update-cart', updatedCart)
    },
    
    clearCart() {
      if (confirm('¿Estás seguro de que quieres vaciar el carrito?')) {
        this.$emit('clear-cart')
      }
    },
    
    async checkout() {
      try {
        for (const item of this.cartItems) {
          const newStock = item.product.productstock - item.quantity
          await fetch(`http://localhost:4000/Products/${item.product.id}`, {
            method: 'PATCH',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ productstock: newStock })
          })
        }
        
        this.showSuccessModal = true
        
        this.$emit('clear-cart')
        
      } catch (error) {
        console.error('Error durante la compra:', error)
        alert('Error al procesar la compra. Intenta nuevamente.')
      }
    },
    
    closeSuccessModal() {
      this.showSuccessModal = false
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>
.empty-cart {
  padding: 4rem 2rem;
}

.cart-summary {
  position: sticky;
  top: 2rem;
}

.summary-details {
  margin-bottom: 1.5rem;
}

.total {
  font-size: 1.2rem;
  color: #013369;
}

.cart-actions .btn {
  padding: 0.75rem;
}

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
  max-width: 500px;
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

.modal-body-custom {
  padding: 3rem 2rem;
}

.bi-check-circle-fall {
  color: #28a745;
}
</style>