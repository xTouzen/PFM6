<template>
  <div class="product-cart-item card mb-3">
    <div class="card-body">
      <div class="row align-items-center">
        <div class="col-md-2">
          <img 
            :src="getImageUrl(item.product.productimage)" 
            :alt="item.product.productname"
            class="img-fluid rounded"
          >
        </div>

        <div class="col-md-4">
          <h5 class="product-name">{{ item.product.productname }}</h5>
          <p class="product-price mb-1">${{ formatPrice(item.product.productprice) }}</p>
          <small class="text-muted">Stock: {{ item.product.productstock }}</small>
        </div>
        
        <div class="col-md-3">
          <div class="quantity-controls">
            <label class="form-label">Cantidad:</label>
            <div class="input-group" style="max-width: 140px;">
              <button 
                class="btn btn-outline-secondary" 
                type="button" 
                @click="decreaseQuantity"
                :disabled="item.quantity <= 1"
              >
                <i class="bi bi-dash"></i>
              </button>
              <input 
                type="number" 
                class="form-control text-center" 
                v-model.number="localQuantity"
                min="1"
                :max="item.product.productstock"
                @change="updateQuantity"
              >
              <button 
                class="btn btn-outline-secondary" 
                type="button" 
                @click="increaseQuantity"
                :disabled="item.quantity >= item.product.productstock"
              >
                <i class="bi bi-plus"></i>
              </button>
            </div>
          </div>
        </div>
        
        <div class="col-md-3 text-end">
          <div class="subtotal mb-2">
            <strong>${{ formatPrice(item.product.productprice * item.quantity) }}</strong>
          </div>
          <button 
            class="btn btn-outline-danger btn-sm"
            @click="removeItem"
          >
            <i class="bi bi-trash"></i> Eliminar
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductCart',
  props: {
    item: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      localQuantity: this.item.quantity
    }
  },
  watch: {
    'item.quantity'(newVal) {
      this.localQuantity = newVal
    }
  },
  methods: {
    getImageUrl(imagePath) {
      return imagePath.startsWith('/') ? imagePath : `/${imagePath}`
    },
    
    formatPrice(price) {
      return new Intl.NumberFormat('es-CL').format(price)
    },
    
    increaseQuantity() {
      if (this.localQuantity < this.item.product.productstock) {
        this.localQuantity++
        this.updateQuantity()
      }
    },
    
    decreaseQuantity() {
      if (this.localQuantity > 1) {
        this.localQuantity--
        this.updateQuantity()
      }
    },
    
    updateQuantity() {
      if (this.localQuantity < 1) {
        this.localQuantity = 1
      } else if (this.localQuantity > this.item.product.productstock) {
        this.localQuantity = this.item.product.productstock
      }
      
      this.$emit('update-quantity', this.item.product.id, this.localQuantity)
    },
    
    removeItem() {
      if (confirm('¿Estás seguro de que quieres eliminar este producto del carrito?')) {
        this.$emit('remove-item', this.item.product.id)
      }
    }
  }
}
</script>

<style scoped>
.product-cart-item {
  border: none;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  transition: box-shadow 0.3s ease;
}

.product-cart-item:hover {
  box-shadow: 0 4px 15px rgba(0,0,0,0.15);
}

.product-name {
  font-weight: 600;
  color: #333;
  margin-bottom: 0.5rem;
}

.product-price {
  font-size: 1.1rem;
  font-weight: 700;
  color: #013369;
}

.quantity-controls .input-group {
  margin-top: 0.25rem;
}

.quantity-controls .btn {
  width: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quantity-controls input {
  border-left: none;
  border-right: none;
}

.subtotal {
  font-size: 1.2rem;
  color: #013369;
}

.btn-outline-danger {
  border-color: #dc3545;
  color: #dc3545;
}

.btn-outline-danger:hover {
  background-color: #dc3545;
  color: white;
}

@media (max-width: 768px) {
  .product-cart-item .row > div {
    margin-bottom: 1rem;
  }
  
  .product-cart-item .text-end {
    text-align: left !important;
  }
}
</style>