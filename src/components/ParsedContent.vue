<template>
  <div class="parsed-content">
    <h2>{{ content.title }}</h2>
    <div v-for="(paragraph, index) in content.paragraphs" :key="index">
      <p>{{ paragraph }}</p>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  content: {
    type: Object,
    required: true,
  },
});
</script>

<style scoped>
.parsed-content {
  margin-top: 20px;
}
</style>
