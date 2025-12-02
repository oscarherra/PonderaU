<template>
  <div class="ad-wrapper">
   <!-- Calculadora -->
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="ca-pub-3698639587251750"
     data-ad-slot="5654559036"></ins>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  try {
    (window.adsbygoogle = window.adsbygoogle || []).push({})
  } catch (e) {
    console.warn("Adsense error:", e)
  }
})
</script>

<style scoped>
.ad-wrapper {
  margin-top: 1rem;
  display: flex;
  justify-content: center;
}
</style>
