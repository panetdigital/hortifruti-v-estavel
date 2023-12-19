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
                auto-select-first
                class="flex-full-width"
                density="comfortable"
                item-props
                menu-icon=""
                placeholder="Pesquisar Produto"
                prepend-inner-icon="mdi-magnify"
                rounded
                theme="light"
                variant="solo"
        
                

                v-model="selectedFruit"
                :items="filteredFruits"
                label="Pesquisar frutas"
                item-text="nome"
                @change="handleFruitSelection"
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
import fruitsData from "@/assets/fruits.json"; // Importe seus dados JSON

export default {
  name: 'IndexPage',
  data() {
    return {
      selectedFruit: null,
      fruits: [],
      searchResults: [],
    };
  },
  computed: {
    filteredFruits() {
      if (!this.selectedFruit) {
        return this.fruits;
      }

      return this.fruits.filter((fruit) =>
        fruit.nome.toLowerCase().includes(this.selectedFruit.toLowerCase())
      );
    },
  },
  methods: {
    async fetchFruitsFromApi() {
      try {
        /* const response = await fetch('http://54.233.235.158/api/produtos'); */ // Substitua pela URL real

        const response = await fetch('https://api.marketingonline.click/api/produtos');
        const data = await response.json();
        this.fruits = data;
      } catch (error) {
        console.error('Erro ao buscar frutas da API:', error);
      }
    },
    handleFruitSelection() {
      if (this.selectedFruit) {
        const query = this.selectedFruit.toLowerCase();
        this.searchResults = this.fruits.filter((fruit) =>
          fruit.nome.toLowerCase().includes(query) 
        );

        // Limpe o campo de input (autocomplete) e redefina os resultados
        this.selectedFruit = null;
      } else {
        this.searchResults = [];
      }
    },
  },
  created() {
    this.fetchFruitsFromApi();
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