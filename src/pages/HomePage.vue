<template>
  <div>
    <h1>Dashboard</h1>
    <div class="overview">
      <p>Total de produtos: {{ totalProducts }}</p>
      <p>Total de categorias: {{ totalCategories }}</p>
      <p>Total de pedidos: {{ totalOrders }}</p>
      <p>Total de usuários: {{ totalUsers }}</p>
    </div>

    <!-- Botões de navegação -->
    <div class="buttons">
      <button @click="goTo('/products')">Produtos</button>
      <button @click="goTo('/orders')">Pedidos</button>
      <button @click="goTo('/users')">Usuários</button>
      <button @click="goTo('/')">SAIR</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      totalProducts: 0,
      totalCategories: 0,
      totalOrders: 0,
      totalUsers: 0,
    };
  },
  async created() {
    const products = await axios.get('https://fakestoreapi.com/products');
    const categories = await axios.get('https://fakestoreapi.com/products/categories');
    const carts = await axios.get('https://fakestoreapi.com/carts');
    const users = await axios.get('https://fakestoreapi.com/users');

    this.totalProducts = products.data.length;
    this.totalCategories = categories.data.length;
    this.totalOrders = carts.data.length;
    this.totalUsers = users.data.length;
  },
  methods: {
    goTo(route) {
      this.$router.push(route); // Navega para a rota especificada
    },
  },
};
</script>

<style scoped>
/* Estilo simples para os botões */
.buttons {
  margin-top: 20px;
}

button {
  margin: 10px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

button:hover {
  background-color: #f0f0f0;
}
</style>
