<!-- pages/gestao-inventario.vue -->
<template>
  <v-container>
    <v-row>
      <v-col>
       


        <!-- <v-h1>Listado de Productos</v-h1> -->

        <ProductList :products="products" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>


import ProductList from '@/components/ProductList.vue';
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      
    };
  },
  components: {
    ProductList,
  },
  created() {
    this.fetchProducts();
  },
  methods: {
  
    fetchProducts() {
      const apiUrl = 'http://54.207.231.105:8989/api/produtos';

      axios.get(apiUrl)
        .then(response => {
          this.products = response.data;
          
        
        })
        .catch(error => {
          console.error('Erro ao buscar produtos:', error);
        });
    },
  },
};
</script>

<style scoped>
/* Adicione estilos específicos do Vuetify aqui, se necessário */
</style>
