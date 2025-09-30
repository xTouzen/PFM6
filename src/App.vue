<template>
  <main>
    <div id="app">
      <Navbar :cartItems="cart" @update-cart="updateCart" />
      <router-view
        :cartItems="cart"
        @add-to-cart="addToCart"
        @update-cart="updateCart"
        @clear-cart="clearCart"
      />
    </div>
  </main>
  <footer class="bg-dark text-light py-4 mt-5">
    <div class="container text-center">
      <p class="mb-0">&copy; 2025 NFL Shop. Todos los derechos reservados.</p>
    </div>
  </footer>
</template>

<script>
import Navbar from "./components/Navbar.vue";

export default {
  name: "App",
  components: {
    Navbar,
  },
  data() {
    return {
      cart: [],
    };
  },
  created() {
    const savedCart = localStorage.getItem("nflShopCart");
    if (savedCart) {
      this.cart = JSON.parse(savedCart);
    }
  },
  methods: {
    addToCart(item) {
      const existingItem = this.cart.find(
        (cartItem) => cartItem.product.id === item.product.id
      );

      if (existingItem) {
        existingItem.quantity += item.quantity;
      } else {
        this.cart.push(item);
      }

      this.saveCartToLocalStorage();
    },

    updateCart(updatedCart) {
      this.cart = updatedCart;
      this.saveCartToLocalStorage();
    },

    clearCart() {
      this.cart = [];
      this.saveCartToLocalStorage();
    },

    saveCartToLocalStorage() {
      localStorage.setItem("nflShopCart", JSON.stringify(this.cart));
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f8f9fa;
  line-height: 1.6;
}

#app {
  min-height: 100vh;
}
</style>
