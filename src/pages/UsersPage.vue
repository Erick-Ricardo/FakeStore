<template>
  <div class="user-management">
    <h1>Gestão de Usuários</h1>
    <div class="search-container">
      <input v-model="searchQuery" @input="searchUsers" placeholder="Pesquisar por nome ou e-mail" class="search-input" />
    </div>
    
    <div class="user-list">
      <div v-for="user in filteredUsers" :key="user.id" class="user-card">
        <div class="user-image-container">
          <img :src="user.image" alt="Imagem do usuário" class="user-image" />
        </div>
        <div class="user-info">
          <h3>{{ user.name }}</h3>
          <p><strong>E-mail:</strong> {{ user.email }}</p>
          <p><strong>Cidade:</strong> {{ user.address.city }}</p>
        </div>
        <div class="user-actions">
          <button @click="viewUserDetails(user.id)" class="action-btn">Detalhes</button>
          <button @click="editUser(user.id)" class="action-btn">Editar</button>
          <button @click="deleteUser(user.id)" class="action-btn">Excluir</button>
        </div>
      </div>
    </div>

    <div class="add-user-container">
      <button @click="addUser" class="add-user-btn">Adicionar Novo Usuário</button>
    </div>

    <!-- Botões de navegação -->
    <div class="navigation-buttons">
      <button @click="navigateTo('/home')">Voltar</button>
      <button @click="navigateTo('/')">SAIR</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      users: [],
      filteredUsers: [],
      searchQuery: '',
      newUser: { name: '', email: '', address: { city: '' }, image: '' },
    };
  },
  async created() {
    try {
      const response = await axios.get('https://fakestoreapi.com/users');
      this.users = response.data;
      // Adicionando imagens para cada usuário
      this.users = this.users.map(user => ({
        ...user,
        image: `https://randomuser.me/api/portraits/men/${user.id}.jpg` // Exemplo de imagem associada ao usuário
      }));
      this.filteredUsers = this.users;
    } catch (error) {
      console.error("Erro ao buscar usuários: ", error);
    }
  },
  methods: {
    searchUsers() {
      this.filteredUsers = this.users.filter(user => {
        const name = typeof user.name === 'string' ? user.name.toLowerCase() : ''; // Verifica se 'name' é uma string
        const email = typeof user.email === 'string' ? user.email.toLowerCase() : ''; // Verifica se 'email' é uma string
        return (
          name.includes(this.searchQuery.toLowerCase()) ||
          email.includes(this.searchQuery.toLowerCase())
        );
      });
    },
    viewUserDetails(id) {
      const user = this.users.find(u => u.id === id);
      if (user) {
        alert(`Detalhes do usuário: \nNome de Usuario: ${user.username}\nE-mail: ${user.email}\nCidade: ${user.address.city}`);
      }
    },
    editUser(id) {
      const user = this.users.find(u => u.id === id);
      if (user) {
        this.newUser = { ...user };  // Preenche o formulário com os dados do usuário
        alert(`Editando: ${user.name}`);
      }
    },
    async deleteUser(id) {
      try {
        const userIndex = this.users.findIndex(u => u.id === id);
        if (userIndex !== -1) {
          this.users.splice(userIndex, 1); // Remove o usuário da lista local
          this.filteredUsers = [...this.users]; // Atualiza a lista filtrada
          await axios.delete(`https://fakestoreapi.com/users/${id}`);
          alert('Usuário excluído com sucesso.');
        }
      } catch (error) {
        console.error('Erro ao excluir o usuário:', error);
        alert('Erro ao excluir o usuário.');
      }
    },
    async addUser() {
      try {
        const newUser = { ...this.newUser };
        const response = await axios.post('https://fakestoreapi.com/users', newUser);
        this.users.push(response.data);
        this.filteredUsers = [...this.users];
        this.newUser = { name: '', email: '', address: { city: '' }, image: '' }; // Limpa os campos do formulário
        alert('Novo usuário adicionado com sucesso!');
      } catch (error) {
        console.error('Erro ao adicionar usuário:', error);
        alert('Erro ao adicionar o usuário.');
      }
    },
    navigateTo(route) {
      this.$router.push(route); // Navegar para a rota especificada
    },
  },
};
</script>

<style scoped>
h1 {
  color: #f0f0f0;
}
/* Estilo geral do container */
.user-management {
  font-family: Arial, sans-serif;
  padding: 20px;
  background-color: #8a2323;
}

/* Estilo do campo de pesquisa */
.search-container {
  margin-bottom: 20px;
  text-align: center;
}

.search-input {
  width: 60%;
  padding: 10px;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid #ddd;
  outline: none;
}

.search-input:focus {
  border-color: #007BFF;
}

/* Lista de usuários */
.user-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.user-card {
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 250px;
  margin: 15px;
  text-align: center;
  padding: 15px;
}

.user-image-container {
  margin-bottom: 15px;
}

.user-image {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  object-fit: cover;
}

.user-info h3 {
  font-size: 20px;
  margin: 10px 0;
}

.user-info p {
  font-size: 14px;
  color: #555;
}

.user-actions {
  margin-top: 10px;
}

.action-btn {
  background-color: #007BFF;
  color: white;
  border: none;
  padding: 8px 15px;
  border-radius: 5px;
  cursor: pointer;
  margin: 5px;
  font-size: 14px;
}

.action-btn:hover {
  background-color: #0056b3;
}

/* Estilo do botão de adicionar usuário */
.add-user-container {
  text-align: center;
  margin-top: 20px;
}

.add-user-btn {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.add-user-btn:hover {
  background-color: #218838;
}

/* Estilo dos botões de navegação */
.navigation-buttons {
  margin-top: 30px;
  display: flex;
  justify-content: space-around;
}

.navigation-buttons button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  border-radius: 5px;
  border: none;
}

.navigation-buttons button:hover {
  background-color: #f0f0f0;
}
</style>
