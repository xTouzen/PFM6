<template>
  <div id="home">
    <ProductList @add-to-cart="$emit('add-to-cart', $event)"/>
  </div>
</template>

<script>
import ProductList from '../components/ProductList.vue'

export default {
  name: 'HomeView',
  components: {
    ProductList
  }
}
</script>