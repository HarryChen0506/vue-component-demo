<template>
  <div class="demo">
    <h1>This is an demo page</h1>
    <div class="demo-main">
      <image-sticker></image-sticker> 
    </div>     
  </div>
</template>
<script>
import ImageSticker from '@/components/ImageSticker.vue'
export default {
  components: {
    ImageSticker
  },
  data: function () {
    return {      
    }
  },
  methods: {
  }
}
</script>
<style>
.demo-main {
  position: relative;
  width: 600px;
  height: 400px;
  border: 1px solid red;
}
</style>
