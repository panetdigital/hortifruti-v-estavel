<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">

      <v-card-title class="text-h6 text-md-h5 text-lg-h4">
       <h4 style="color: green;">{{ typedText }}</h4>
      </v-card-title>
  
        <v-spacer />
        
        <div >
          
           <strong><p>Campo de Pesquisa só OVO:</p></strong>

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
                  <div>{{ item.nome }}</div>
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

<!-- Preço- resultado -->
         <div v-if="selectedProductPrice" class="preco">
          Preço:  <strong>{{ selectedProductPrice }}</strong>
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
import axios from 'axios';
import data from "@/assets/ovos.json";

export default {
  name: 'IndexPage',
  data() {
    return {
      selectedProductCod: '', // Alteração do nome da variável para representar o código
      selectedProductPrice: null,
      products: data,
      searchResults: [],
    };
  },
  computed: {
    filteredProducts() {
      if (!this.selectedProductCod) {
        return this.products;
      }

      return this.products.filter((product) =>
        product.cod.includes(this.selectedProductCod)
      );
    },
  },
  methods: {
    determineCategoria(data) {
      if (this.selectedProductCod) {
        const selectedProduct = data.find(product => product.cod === this.selectedProductCod);

        if (selectedProduct && selectedProduct.grupo) {
          console.log('Categoria do Produto Selecionado:', selectedProduct.grupo);
          return selectedProduct.grupo;
        }
      }

      console.log('Nenhuma Categoria Encontrada para o Produto Selecionado');
      return '';
    },

    async handleSearchInput() {
      if (!this.selectedProductCod || this.selectedProductCod.length < 2) {
        this.searchResults = [];
        return;
      }

      console.log('this.selectedProductCod antes da requisição à API:', this.selectedProductCod);

      try {
        const response = await axios.get(`http://15.228.155.150:3000/api/ovos/${encodeURIComponent(this.selectedProductCod)}`);

        console.log('Resposta da API:', response.data);

        if (response.data) {
          const categoriaDoProduto = this.determineCategoria(this.products);

          console.log('Categoria do Produto:', categoriaDoProduto);

          if (categoriaDoProduto) {
            let novaResposta;

            if (categoriaDoProduto.toLowerCase() === 'ovo') {
              novaResposta = await axios.get(`http://15.228.155.150:3000/api/ovos/${encodeURIComponent(this.selectedProductCod)}`);
            }

            console.log('Resposta da Nova API com base na Categoria:', novaResposta.data);

            const { nome, preco } = novaResposta.data;

            this.selectedProductPrice = preco;
          } else {
            console.log('Nenhuma categoria encontrada para fazer a nova requisição.');
          }
        } else {
          console.log('Nenhum produto encontrado com o código fornecido ou dados incompletos na resposta.');
        }
      } catch (error) {
        console.error('Erro ao fazer a requisição à API:', error);
      }

      const query = this.selectedProductCod;
      this.searchResults = this.products.filter((product) =>
        product.cod.includes(query)
      );
    }
  },
  created() {
    this.handleSearchInput();
  },
};
</script>


<style>
.preco {
  font-weight: bold;
  font-size: 22px;
}
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