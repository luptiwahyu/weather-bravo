<script setup lang="ts">
import { ref } from 'vue'

const title = ref('Hello Vue!')

setTimeout(() => {
  title.value = 'Gracias!!'
}, 2000)
</script>

<template>
  <div>
    <h1>{{ title }}</h1>
    <p>
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Dolorem temporibus eligendi dicta sit inventore quam
      nostrum tempore repellat quas a repellendus maxime amet dignissimos explicabo blanditiis, maiores dolore rem
      consequatur.
    </p>
  </div>
</template>

<style scoped>
p {
  color: green;
}
</style>
