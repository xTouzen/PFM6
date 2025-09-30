<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-white">
    <div class="container">

      <router-link class="navbar-brand" to="/">
        <img 
          :src="logo" 
          alt="NFL Shop" 
          class="navbar-logo"
        >
      </router-link>
      
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
        <span class="navbar-toggler-icon"></span>
      </button>
      
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto align-items-center">

          <li v-if="currentUser" class="nav-item welcome-message">
            <span class="navbar-text">Bienvenido <strong>{{ currentUser.username}}</strong></span>
          </li>

          <li class="nav-item">
            <router-link class="nav-link cart-icon" to="/cart">
              <div class="cart-indicator">
                <i class="bi bi-cart3"></i>
                <span v-if="cartItems.length > 0" class="cart-badge">
                  {{ totalCartItems }}
                </span>
              </div>
            </router-link>
          </li>

          <li class="nav-item">
            <a 
              v-if="currentUser" 
              class="nav-link btn-logout" 
              href="/login" 
              @click="handleLogout"
            >
              <i class="bi bi-box-arrow-right me-1"></i>Logout
            </a>

            <router-link 
              v-else 
              to="/login" 
              class="nav-link px-0">
              <button class="btn btn-outline-primary btn-sm rounded-pill px-3">Login</button>
            </router-link>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script>
import logo from '@/assets/logos/nflshop-logo.png'

export default {
  name: 'Navbar',
    props: {
    cartItems: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      logo: logo,
      currentUser: null
    }
  },
  computed: {
    totalCartItems() {
      return this.cartItems.reduce((total, item) => total + item.quantity, 0)
    }
  },
  methods: {
    handleLogout() {
      this.currentUser = null
      localStorage.removeItem('user')
      window.dispatchEvent(new CustomEvent('user-logout'))

      if (this.$route.path !== '/') {
        this.$router.push('/')
      }
    },
    checkAuth() {
      const savedUser = localStorage.getItem('user')
      if (savedUser) {
        this.currentUser = JSON.parse(savedUser)
      }
    }
  },
  mounted() {
    this.checkAuth()
    
    window.addEventListener('user-login', (event) => {
      this.currentUser = event.detail
    })
    
    window.addEventListener('user-logout', () => {
      this.currentUser = null
    })
  }
}
</script>

<style scoped>
.navbar-logo {
  height: 55px;
  width: auto;
  transition: transform 0.3s ease;
}

.navbar-logo:hover {
  transform: scale(1.05);
}

.navbar {
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  padding: 1.2rem 0;
  min-height: 80px;
}

.welcome-message {
  display: flex;
  align-items: center;
  margin-right: 1rem;
  color: #013369;
  font-weight: 500;
}

.navbar-text {
  color: #013369;
  font-size: 0.95rem;
}

.cart-icon {
  position: relative;
  padding: 0.5rem 1rem !important;
  margin-right: 0.5rem;
}

.cart-indicator {
  position: relative;
  font-size: 1.2rem;
  color: #013369;
}

.cart-badge {
  position: absolute;
  top: -8px;
  right: -8px;
  background-color: #D50A0A;
  color: white;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  font-size: 0.7rem;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
}

.nav-link {
  font-weight: 500;
  transition: color 0.3s ease;
}

.btn-login {
  background-color: #013369;
  color: white;
  border-radius: 20px;
  padding: 10px 22px;
  transition: all 0.3s ease;
  font-weight: 500;
}

.btn-login:hover {
  background-color: #D50A0A;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
.btn-logout {
  background-color: #6c757d;
  color: white;
  border-radius: 20px;
  padding: 10px 22px;
  transition: all 0.3s ease;
  font-weight: 500;
}

.btn-logout:hover {
  background-color: #D50A0A;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
.navbar-nav {
  align-items: center;
}

@media (max-width: 768px) {
  .welcome-message {
    margin-right: 0;
    margin-bottom: 0.5rem;
    justify-content: center;
  }
  
  .navbar-nav {
    text-align: center;
  }
  
  .cart-icon {
    margin-right: 0;
    margin-bottom: 0.5rem;
  }
}
</style>