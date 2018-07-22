<template>
  <div class="demo">
    <h1>This is an demo page</h1>
    <!-- <pre>{{sticker}}</pre> -->
    <div class="demo-main">      
      <image-sticker :state="sticker"></image-sticker> 
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
      sticker: {}     
    }
  },
  methods: {
  },
  mounted: function () {
    setTimeout(() => {
      this.sticker = {
        width: 100,
        height: 100,
        left: 50,
        top: 50,
        rotate: 10,
        zIndex: 2
      }
      console.log('sticker', this.sticker)
    }, 1000)
  }
}
</script>
<style>
.demo-main {
  position: relative;
  width: 300px;
  height: 400px;
  border: 1px solid red;
}
</style>
