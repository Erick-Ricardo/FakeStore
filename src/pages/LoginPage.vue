<template>
  <div class="login-container">
    <h1>Login</h1>
    <form @submit.prevent="login">
      <div>
        <input 
          v-model="email" 
          type="email" 
          placeholder="Digite seu e-mail" 
          required 
        />
      </div>
      <div>
        <input 
          v-model="password" 
          type="password" 
          placeholder="Digite sua senha" 
          required 
        />
      </div>
      <button type="submit">Entrar</button>
    </form>
    <div v-if="errorMessage" class="error">{{ errorMessage }}</div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      email: '',
      password: '',
      errorMessage: '',
    };
  },
  methods: {
    async login() {
      try {
        // Faz uma requisição para pegar a lista de usuários
        const response = await axios.get('https://fakestoreapi.com/users');
        const users = response.data;

        // Exibe os usuários no console para depuração
        console.log('Usuários:', users);

        // Verifica se o usuário existe com o e-mail fornecido
        const user = users.find(user => user.email.toLowerCase() === this.email.toLowerCase());

        if (user) {
          // Verifica se a senha informada corresponde à senha do usuário
          if (this.password === user.password) {
            // Se a senha for válida, vamos gerar um token fictício
            const fakeToken = 'fake-jwt-token-123456'; // Token fictício

            // Armazena o token no localStorage
            localStorage.setItem('token', fakeToken);

            // Redireciona para a página inicial (home)
            this.$router.push('/home');
          } else {
            this.errorMessage = 'Senha inválida. Tente novamente.';
          }
        } else {
          this.errorMessage = 'E-mail não encontrado. Tente novamente.';
        }
      } catch (error) {
        this.errorMessage = 'Erro ao autenticar. Tente novamente mais tarde.';
      }
    },
  },
};
</script>

<style scoped>
.login-container {
  width: 300px;
  margin: 0 auto;
  padding: 50px;
  border: 1px solid #ccc;
  border-radius: 10px;
  text-align: center;
}
input {
  width: 100%;
  padding: 10px;
  margin: 10px 0;
}
button {
  width: 100%;
  padding: 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
.error {
  color: red;
  font-size: 14px;
}
</style>
