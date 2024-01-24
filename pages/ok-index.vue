<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">

      <v-card-title class="text-h6 text-md-h5 text-lg-h4">
       <h4 style="color: green;">{{ typedText }}</h4>
      </v-card-title>
  
        <v-spacer />
        
        <div >
          
           <strong><p>Campo de Pesquisa:</p></strong>

           <v-autocomplete
                  append-inner-icon="mdi-microphone"
                  class="flex-full-width"
                  density="comfortable"
                  item-props
                  placeholder="Pesquisar Produto"
                  prepend-inner-icon="mdi-magnify"
                  rounded
                  theme="light"
                  variant="solo"
                  
                  v-model="selectedProduct"
                  :items="filteredProducts"
                  label="Pesquisar produtos"
                  item-text="nome"
                  @input="handleSearchInput"
                  clearable
                  :search-input="selectedProduct"
                  menu-props="auto" 
                >

                <template v-slot:item="{ item }">
                  <div>{{ item.nome }} - Código: {{ item.cod }}</div>
                </template>
            </v-autocomplete>
<!-- este mtd vai ser apagado -->
           <!-- @input="filterProducts" -->

              <div>
                
                <div v-if="searchResults.length === 0">
                  <!-- usar campo de pesquisar. -->
                  <br>
                </div>
                <div v-else>
                  <h3>Resultados da Pesquisa:</h3>
                  <v-table   class="result-table">
                    <thead>
                      <tr>
                        <th>Código</th>
                        <th>Nome</th>
                        <th>Imagem</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="result in searchResults" :key="result.id">
                        <td>{{ result.coditem }}</td>
                        <td>{{ result.nome }}</td>
                        <td><img :src="result.imagem" alt="Imagem do Produto" class="product-image" /></td>
                      </tr>
                    </tbody>
                  </v-table>
                </div>
              </div>
         </div>

          <!-- botao home -->

        <v-card flat>
          <v-spacer />
          <v-btn
            color="primary"
            nuxt
            to="/listatarefa"
          >
           Criar Tarefa
          </v-btn>
        </v-card>
      
    </v-col>
  </v-row>
</template>

 <!-- Efeito escrever -->
<script setup>
import { ref, onMounted } from 'vue';

const originalText = "Bem-vindo! pesquisar produto"; // Seu texto original
const typedText = ref('');
const typingSpeed = 100; // Velocidade de digitação em milissegundos

onMounted(() => {
  startTyping();
});

function startTyping() {
  let index = 0;
  const intervalId = setInterval(() => {
    typedText.value += originalText[index];
    index++;

    if (index === originalText.length) {
      clearInterval(intervalId);
    }
  }, typingSpeed);
}
</script>
<!-- Fim Efeito escrever -->


<script>
import data from "@/assets/fruits.json"; // Importe seus dados JSON

export default {
  name: 'IndexPage',
  data() {
    return {
      selectedProduct: '',
      products: data,
      searchResults: [],

    };
  },
  computed: {
  filteredProducts() {
    if (!this.selectedProduct) {
      return this.products;
    }

    return this.products.filter((product) =>
      product.nome.toLowerCase().includes(this.selectedProduct.toLowerCase())
    );
  },
},

methods: {
  handleSearchInput() {
    // Verifique se o texto digitado tem pelo menos 2 caracteres
    if (this.selectedProduct && this.selectedProduct.length >= 2) {
      // Filtre os produtos baseados no texto digitado
      const query = this.selectedProduct.toLowerCase();
      this.searchResults = this.products.filter((product) =>
        product.nome.toLowerCase().includes(query)
      );
    } else {
      // Limpe os resultados se não houver pelo menos 2 caracteres
      this.searchResults = [];
    }
  },
},


  created() {
    this.handleSearchInput();
  },
};
</script>

<style>

.product-info {
  display: flex;
  align-items: center;
}

.product-image {
  max-width: 100px;
  max-height: 100px;
  margin-right: 10px;
}

.result-table {
  border-collapse: collapse;
  width: 100%;
}

.result-table th, .result-table td {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 20px; /* aumentei para 20px era 8px */
}

.result-table th {
  background-color: #f2f2f2;
}

</style>
<style scoped>
.flex-full-width {
  background-color: rgb(190, 190, 202); /* Cor de fundo personalizada */
  color: rgb(19, 2, 2); /* Cor do texto personalizada */
}

</style>