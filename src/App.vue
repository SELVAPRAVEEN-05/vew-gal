<template>
  <div id="app">
    <ImageGallery />
  </div>
</template>

<script>
import ImageGallery from './components/imageGallary.vue';

export default {
  name: 'App',
  components: {
    ImageGallery,
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
