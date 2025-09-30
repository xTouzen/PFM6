<template>
  <div class="login-container">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-md-6 col-lg-4">
          <div class="card login-card">
            <div class="card-body">
              <div class="text-center mb-4">
                <img 
                  :src="logo" 
                  alt="NFL Shop" 
                  class="login-logo"
                >
                <h2 class="login-title">Iniciar Sesión</h2>
              </div>

              <form @submit.prevent="handleLogin">
                <div class="mb-3">
                  <label for="username" class="form-label">Usuario</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="username"
                    v-model="credentials.username"
                    required
                    placeholder="Ingresa tu usuario"
                  >
                </div>

                <div class="mb-4">
                  <label for="password" class="form-label">Contraseña</label>
                  <input 
                    type="password" 
                    class="form-control" 
                    id="password"
                    v-model="credentials.password"
                    required
                    placeholder="Ingresa tu contraseña"
                  >
                </div>

                <button 
                  type="submit" 
                  class="btn btn-primary w-100 login-btn"
                  :disabled="loading"
                >
                  <span v-if="loading" class="spinner-border spinner-border-sm me-2"></span>
                  {{ loading ? 'Iniciando sesión...' : 'Iniciar Sesión' }}
                </button>

                <div v-if="error" class="alert alert-danger mt-3 mb-0">
                  {{ error }}
                </div>
              </form>

              <div class="demo-credentials mt-4">
                <p class="text-muted small text-center">
                  <strong>Demo:</strong> usuario: <code>admin</code> / contraseña: <code>admin</code>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import logo from '@/assets/logos/nflshop-logo.png'

export default {
  name: 'LoginView',
  data() {
    return {
      logo: logo,
      credentials: {
        username: '',
        password: ''
      },
      loading: false,
      error: ''
    }
  },
  methods: {
    async handleLogin() {
      this.loading = true
      this.error = ''

      try {
        await new Promise(resolve => setTimeout(resolve, 1000))
        
        const response = await fetch('http://localhost:4000/users')
        const users = await response.json()

        const user = users.find(u => 
          u.username === this.credentials.username && 
          u.password === this.credentials.password
        )

        if (user) {
          localStorage.setItem('user', JSON.stringify(user))
          
          window.dispatchEvent(new CustomEvent('user-login', { detail: user }))
          
          this.$router.push('/')
        } else {
          this.error = 'Usuario o contraseña incorrectos'
        }
      } catch (error) {
        console.error('Error en login:', error)
        this.error = 'Error al conectar con el servidor'
      } finally {
        this.loading = false
      }
    }
  },
  mounted() {
    if (localStorage.getItem('user')) {
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>
.login-container {
  min-height: 80vh;
  display: flex;
  align-items: center;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  padding: 2rem 0;
}

.login-card {
  border: none;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.1);
  overflow: hidden;
}

.login-logo {
  height: 60px;
  width: auto;
  margin-bottom: 1rem;
}

.login-title {
  color: #013369;
  font-weight: 700;
  margin-bottom: 0;
}

.form-label {
  font-weight: 600;
  color: #495057;
  margin-bottom: 0.5rem;
}

.form-control {
  border: 2px solid #e9ecef;
  border-radius: 8px;
  padding: 0.75rem 1rem;
  transition: all 0.3s ease;
}

.form-control:focus {
  border-color: #013369;
  box-shadow: 0 0 0 0.2rem rgba(1, 51, 105, 0.25);
}

.login-btn {
  background: linear-gradient(135deg, #013369, #0155a3);
  border: none;
  border-radius: 8px;
  padding: 0.75rem;
  font-weight: 600;
  font-size: 1.1rem;
  transition: all 0.3s ease;
}

.login-btn:hover:not(:disabled) {
  background: linear-gradient(135deg, #D50A0A, #ff2a2a);
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(213, 10, 10, 0.3);
}

.login-btn:disabled {
  background: #6c757d;
  transform: none;
  box-shadow: none;
}

.demo-credentials {
  border-top: 1px solid #e9ecef;
  padding-top: 1rem;
}

.alert {
  border: none;
  border-radius: 8px;
  padding: 0.75rem 1rem;
}
</style>