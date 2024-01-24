<!-- pages/index.vue -->

<template>
  <div>
    <BarcodeScanner />
  </div>
</template>

<script>
import BarcodeScanner from '~/components/BarcodeScanner.vue';

export default {
  components: {
    BarcodeScanner,
  },
};
</script>
