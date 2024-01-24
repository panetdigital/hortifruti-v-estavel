<template>
  <div>
    <div>
      <button
        v-for="group in uniqueGroups"
        :key="group"
        @click="filterByGroup(group)"
        class="filter-button"
      >
        {{ group }}
      </button>
      <button @click="clearFilter" class="clear-filter-button">Limpar Filtro</button>
    </div>

    <div>
    <label for="input-date">Data de Entrada:</label>
    <input id="input-date" type="date" v-model="editedProduct.datainventario" />
  </div>

    <v-toolbar flat color="white">
          <v-toolbar-title class="text-h6 font-weight-bold mx-auto">Gestão de Estoque </v-toolbar-title>
        </v-toolbar>
    <table>
      <thead>
        <tr>
          <th>Nome do Produto</th>
          <th>Qtes Produto</th>
          <th>Disponível</th>
          <th>Grupo</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in filteredProducts" :key="product.id">
          <td>{{ product.nome }}</td>
          <td>{{ product.quantidade }}</td>
          <td>
          <span :style="{ color: product.disponible === 0 ? 'red' : 'green' }">
            {{ product.disponible === 1 ? 'Sim' : 'Não' }}
          </span>
        </td>

          <td>{{ product.grupo }}</td>
          <td>
            <v-icon @click="openEditForm(product)">mdi-pencil</v-icon>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- Boite de Dialog pra Ediçao -->
    <div v-if="editDialog" class="edit-dialog">
      <div class="edit-dialog-content">
        <h2>Editar Produto</h2>
        <div>
        <label for="input-date">Data de Entrada:</label>
        <input id="input-date" type="date" v-model="editedProduct.datainventario" />
      </div>
        <div>
           <label for="edit-nome">Nome do Produto:</label>
           <input id="edit-nome" v-model="editedProduct.nome" />
        </div>
        <div>
           <label for="edit-nome">Qtes do Produto: </label>
           <input id="edit-nome" v-model="editedProduct.quantidade" />
        </div>

        <label for="edit-disponible">Disponível: </label>
        <select id="edit-disponible" v-model="editedProduct.disponible">
          <option :value="0">Não</option>
          <option :value="1">Sim</option>
        </select>
        

        <div class="edit-dialog-actions">
          <button @click="saveChanges">Salvar</button>
          <button @click="closeEditForm">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      editDialog: false,
      editedProduct: {
        id: null,
        nome: '',
        quantidade: '',
        disponible: '',
        grupo: '',
        //teste 
        datainventario:'',
      },
      selectedGroup: null,
    };
  },
  created() {
    this.fetchProducts();
  },
  computed: {
    uniqueGroups() {
      return [...new Set(this.products.map(product => product.grupo))];
    },
    filteredProducts() {
      return this.selectedGroup
        ? this.products.filter(product => product.grupo === this.selectedGroup)
        : this.products;
    },
    getDisponibleLabel() {
      return this.editedProduct.disponible === 1 ? 'Sim' : 'Não';
    },
  },
  methods: {
    fetchProducts() {
      const apiUrl = 'http://54.207.231.105:8989/api/produtos';

      axios
        .get(apiUrl)
        .then(response => {
          this.products = response.data;
        })
        .catch(error => {
          console.error('Erro ao buscar produtos:', error);
        });
    },
    openEditForm(product) {
      this.editedProduct = { ...product };
      this.editDialog = true;
    },
    saveChanges() {
      const apiUrl = `http://54.207.231.105:8989/api/produtos/${this.editedProduct.id}`;

      axios
        .put(apiUrl, this.editedProduct)
        .then(response => {
          console.log('Alterações salvas com sucesso:', response.data);
          this.fetchProducts();
          this.editDialog = false;
        })
        .catch(error => {
          console.error('Erro ao salvar alterações:', error);
        });
    },
    closeEditForm() {
      this.editDialog = false;
    },
    filterByGroup(group) {
      this.selectedGroup = group;
    },
    clearFilter() {
      this.selectedGroup = null;
    },
  },
};
</script>

<style scoped>

.filter-button {
  margin-right: 5px;
  padding: 8px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

/* Estilo para o botão de limpar filtro */
.clear-filter-button {
  margin-right: 5px;
  padding: 8px;
  background-color: #dc3545;
  color: #fff;
  border: none;
  cursor: pointer;
}
.text-h6 {
  font-size: 1.25rem; /* Tamanho do texto */
}

.mx-auto {
  margin-left: auto;
  margin-right: auto;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}

.edit-dialog {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.edit-dialog-content {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.edit-dialog-content label {
  font-weight: bold;
}

.edit-dialog-content input,
.edit-dialog-content select {
  border: 1px solid #ccc;
  padding: 5px;
  margin-bottom: 10px;
}

.edit-dialog-actions {
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
}
</style>
