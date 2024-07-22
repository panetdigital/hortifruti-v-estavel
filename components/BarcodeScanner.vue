<template>
  <v-container fluid fill-height>
    <v-row justify="center" align="center" no-gutters>
      <v-col cols="12" sm="8" md="6" lg="4" class="text-center">
        <h1>Resultado</h1>
        <div v-if="productInfo">
          
          <p style="font-size: 1.5rem;"><strong>Nome:</strong> {{ productInfo.name }}</p>

          
          <p v-if="additionalPrice" style="font-size: 1.5rem;"><strong>Preço:</strong> {{ additionalPrice }}</p>

          <img v-if="productInfo.imagem" :src="productInfo.imagem" :alt="productInfo.name" style="width: 300px; height: 300px;">

          <!-- Adicione um botão para retornar à página inicial -->
          <v-btn @click="redirectToHome" color="primary">Página Inicial</v-btn>
        </div>
        <div ref="barcodeScanner" :style="{ width: '100%', height: productInfo ? '300px' : 'auto' }"></div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Quagga from 'quagga';

export default {
  data() {
    return {
      productInfo: null,
      additionalPrice: null, // Adicione um estado para armazenar o preço adicional
    };
  },
  mounted() {
    this.initializeQuagga();
  },
  methods: {
    initializeQuagga() {
      const barcodeScanner = this.$refs.barcodeScanner;

      if (!barcodeScanner) {
        console.error("Elemento #barcode-scanner não encontrado.");
        return;
      }

      Quagga.init({
        inputStream: {
          name: "Live",
          type: "LiveStream",
          target: barcodeScanner,
          constraints: {
            width: 640,
            height: 480,
            facingMode: "environment",
          },
        },
        decoder: {
          readers: ["ean_reader", "code_128_reader"],
          debug: {
            drawBoundingBox: true,
            showFrequency: true,
            drawScanline: true,
            showPattern: true,
          },
        },
      }, (err) => {
        if (err) {
          console.error(err);
          return;
        }
        Quagga.start();
      });

      Quagga.onDetected((result) => {
        const barcode = result.codeResult.code;
        alert('Código de Barras detectado: ' + barcode);

        Quagga.stop();

        // Pesquise no arquivo camera.json
        this.searchInCameraJson(barcode);
      });
    },
    searchInCameraJson(barcode) {
        // Importa o arquivo camera.json
        const data = require('~/assets/camera.json');

        // Encontre a correspondência do código de barras
        const product = data.find(item => item.cod === barcode);

        if (product) {
          // Atualize as informações do produto no estado
          this.productInfo = {
            name: product.nome,
            imagem: product.imagem,
          };
          //alert(`Produto encontrado: ${product.name} - ${product.price}`);

          // Direcione para o arquivo correspondente com base no grupo
          this.loadAdditionalFile(product.grupo);
        } else {
          //console.error('Produto não encontrado.');
          alert('Produto não encontrado.');

          // Reinicie a leitura de código de barras
        this.initializeQuagga();
        }
},
loadAdditionalFile(grupo) {
  let additionalData;

  switch (grupo) {
    case 'ovo':
      additionalData = require('~/assets/camera.json/raspagem_ovos.json');
      break;
    case 'fruta':
      additionalData = require('../../../nodejs/dadosRaspagem/raspage_frutas.json');
      break;
    case 'legume':
      additionalData = require('../../../nodejs/dadosRaspagem/raspagem_legumes.json');
      break;
    case 'tempero':
      additionalData = require('../../../nodejs/dadosRaspagem/raspagem_temperos.json');
      break;
    case 'verdura':
      additionalData = require('~/assets/camera.jsondadosRaspagem/verduras.json');
      break;
    default:
      alert('Grupo de produto não reconhecido.');
      return;
  }

  // Agora, você pode usar additionalData para carregar os dados do arquivo correspondente
  this.loadAdditionalData(additionalData, this.productInfo.name);
},

loadAdditionalData(additionalData, productName) {
  // Certifique-se de que additionalData é um array
  if (Array.isArray(additionalData) && additionalData.length > 0) {
    // Busque o produto no array adicional com base no nome do produto
    const additionalProduct = additionalData.find(item => item.name.toLowerCase() === productName.toLowerCase());

    if (additionalProduct) {
      const additionalPrice = additionalProduct.price;
      this.additionalPrice = additionalPrice; // Atualize o estado additionalPrice

      alert(`Produto encontrado:\nNome: ${this.productInfo.name}\nPreço: ${additionalPrice}`);

    } else {
      //console.error(`Produto ${productName} não encontrado no arquivo adicional ou formato incorreto.`);
      alert('Erro ao processar dados adicionais. Verifique o console para mais detalhes.');
    }
  } else {
    console.error('Formato do arquivo adicional incorreto ou dados não encontrados.');
    alert('Erro ao carregar dados adicionais. Verifique o console para mais detalhes.');
  }
},
// Função para redirecionar para a página inicial
redirectToHome() {
      // Coloque o caminho real da sua página inicial aqui
      this.$router.push('/');
    }

  },
};
</script>

