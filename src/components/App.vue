<template>
  <div id="app">
    <h1>OOXML Document Parser</h1>
    <UploadFile />
  </div>
</template>

<script>
import UploadFile from './components/UploadFile.vue';

export default {
  components: {
    UploadFile,
  }
};
</script>

<style scoped>
#app {
  text-align: center;
  font-family: Arial, sans-serif;
  margin: 20px;
}
</style>
