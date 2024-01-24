<template>
  <div>
    <v-row>
      <v-col v-for="group in uniqueGroups" :key="group">
        <v-btn @click="filterByGroup(group)">{{ group }}</v-btn>
      </v-col>
      <v-btn @click="clearFilter">Limpar Filtro</v-btn>
    </v-row>

    
    
    <v-data-table
      :headers="headers"
      :items="filteredProducts"
      :items-per-page="10"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-toolbar-title class="text-h6 font-weight-bold mx-auto">Controle de Estoque </v-toolbar-title>
        </v-toolbar>
      </template>
      <template v-slot:item="data">
        
        <tr>
          <td><img :src="data.item.imagem" alt="Imagem" class="thumbnail"></td>
          <td>{{ data.item.nome }}</td>
          <td>{{ data.item.quantidade }}</td>
          <td :class="displayAvailabilityClass(data.item.disponible)">
            {{ displayAvailability(data.item.disponible) }}
          </td>
          <td>{{ data.item.coditem }}</td>
          <!-- <td>{{ data.item.grupo }}</td> -->
        </tr>
      </template>
      <template v-slot:no-data>
        <v-alert :value="true" color="error" icon="mdi-alert">
          Nenhum produto disponível.
        </v-alert>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
  props: {
    products: Array,
  },
  data() {
    return {
      headers: [
      { text: 'FOTO', value: 'Foto' },
        { text: 'PRODUTO ', value: 'Produto' },
        { text: 'QTD DISPO', value: 'Quantidade' },
        { text: 'DISPONIBLE', value: 'Disponível' },
        { text: 'COD BARRA', value: 'Cod Barra' },
       /*  { text: 'Grupo', value: 'grupo' }, */
      ],
      selectedGroup: null,
    };
  },
  computed: {
    uniqueGroups() {
      return [...new Set(this.products.map((product) => product.grupo))];
    },
    filteredProducts() {
      if (!this.selectedGroup) {
        return this.products;
      }
      return this.products.filter(
        (product) => product.grupo.toLowerCase() === this.selectedGroup.toLowerCase()
      );
    },
  },
  methods: {
    displayAvailability(disponible) {
      return disponible ? 'Sim' : 'Não';
    },
    displayAvailabilityClass(disponible) {
      return disponible ? 'text-green' : 'text-red';
    },
    filterByGroup(group) {
      this.selectedGroup = group.toLowerCase();
    },
    clearFilter() {
      this.selectedGroup = null;
    },
  },
};
</script>

<style scoped>
/* Adicione estilos específicos do Vuetify aqui, se necessário */
.text-green {
  color: green;
}

.text-red {
  color: red;
}
v-row {
  margin: 5px;
}

v-col {
  margin: 5px;
}
.text-h6 {
  font-size: 1.25rem; /* Tamanho do texto */
}

.mx-auto {
  margin-left: auto;
  margin-right: auto;
}
.thumbnail {
  width: 50px; /* Ajuste conforme necessário */
  height: auto;
}
</style>
