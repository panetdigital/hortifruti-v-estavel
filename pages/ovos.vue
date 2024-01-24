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
import data from "@/assets/ovos.json"; // Importe seus dados JSON

export default {
  name: 'IndexPage',
  data() {
    return {
      selectedProduct: '',
      selectedProductPrice: null, // Adicionando variável para armazenar o preço
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
  determineCategoria(data) {
    // Verifique se há um produto selecionado
    if (this.selectedProduct) {
      // Encontre o produto correspondente aos dados importados
      const selectedProduct = data.find(product => product.nome === this.selectedProduct);

      // Se o produto for encontrado, retorne a categoria associada a esse produto
      if (selectedProduct && selectedProduct.grupo) {
        console.log('Categoria do Produto Selecionado:', selectedProduct.grupo);
        return selectedProduct.grupo;
      }
    }

    // Se não houver produto selecionado ou não for encontrada a categoria, retorne vazio (ou outra lógica desejada)
    console.log('Nenhuma Categoria Encontrada para o Produto Selecionado');
    return '';
  },

  async handleSearchInput() {
  // Verifique se o texto digitado tem pelo menos 2 caracteres
  if (!this.selectedProduct || this.selectedProduct.length < 2) {
    // Limpe os resultados se não houver pelo menos 2 caracteres
    this.searchResults = [];
    return;
  }

  console.log('this.selectedProduct antes da requisição à API:', this.selectedProduct);

  try {
    // Faça a requisição à API com o Axios
    const response = await axios.get(`http://54.207.231.105:8081/api/ovos/${encodeURIComponent(this.selectedProduct)}`);

    // Log da resposta completa para verificar o que está sendo retornado
    console.log('Resposta da API:', response.data);

    // Verifique se a resposta contém dados válidos
    if (response.data) {
      // Chame determineCategoria após a resposta da API
      const categoriaDoProduto = this.determineCategoria(this.products);

      // Log da categoria do produto para verificar o que está sendo retornado
      console.log('Categoria do Produto:', categoriaDoProduto);

      // Nova requisição com base na categoriaDoProduto
      if (categoriaDoProduto) {
        let novaResposta;

        // Verifica se a categoria é "ovo"
        if (categoriaDoProduto.toLowerCase() === 'ovo') {
          novaResposta = await axios.get(`http://54.207.231.105:8081/produto/ovos/${encodeURIComponent(this.selectedProduct)}`);
        }
       

        console.log('Resposta da Nova API com base na Categoria:', novaResposta.data);

        // Extraia o nome e preço da resposta
        const { name, price } = novaResposta.data;

        // Atualize a variável com o preço para ser exibido no template
        this.selectedProductPrice = price;
      } else {
        console.log('Nenhuma categoria encontrada para fazer a nova requisição.');
      }
    } else {
      console.log('Nenhum produto encontrado com o nome fornecido ou dados incompletos na resposta.');
    }
  } catch (error) {
    // Log de erros para entender possíveis problemas
    console.error('Erro ao fazer a requisição à API:', error);
  }

  // Filtre os produtos baseados no texto digitado
  const query = this.selectedProduct.toLowerCase();
  this.searchResults = this.products.filter((product) =>
    product.nome.toLowerCase().includes(query)
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