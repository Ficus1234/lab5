<template>
<div class="home">
  <div class="some-space"></div>
  <image-gallery class="the-photos" :photos="photos" />
  <p v-if="error">{{error}}</p>
</div>
</template>

<script>
import axios from 'axios';
import ImageGallery from '@/components/ImageGallery.vue';
export default {
  name: 'Home',
  data() {
    return {
      photos: [],
      error: '',
    }
  },
  components: {
    ImageGallery,
  },
  created() {
    this.getPhotos();
  },
  methods: {
    async getPhotos() {
      try {
        let response = await axios.get("/api/photos/all");
        this.photos = response.data;
      } catch (error) {
        this.error = error.response.data.message;
      }
    },
  }
}
</script>
<style>
  .some-space {
    height: 50px;
  }
</style>
