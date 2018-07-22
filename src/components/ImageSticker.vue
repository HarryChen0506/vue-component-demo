<style scoped>
.image-sticker {
  position: absolute;
  box-sizing: border-box;
  border: 1px solid #333
}
</style>

<template>
  <div class="image-sticker" :style="style">
  </div>
</template>
<script>
export default {
  props: {
    config: {
      type: Object,
      default: () => ({
        draggable: true, // 是否可拖拽
        deleteable: true, // 是否可以删除
      }) 
    },
    state: {
      type: Object,
      default: () => ({
        width: 0,
        height: 0,
        left: 0,
        top: 0,
        rotate: 0,
        zIndex: 0,
        isActive: false
      })
    }
  },
  data: function () {
    return {
      name: 'imageSticker',      
      width: 100,
      height: 100,
      left: 10,
      top: 10,
      rotate: 10,
      zIndex: 0,
      isActive: false      
    }
  },
  computed: {
    style () {
      return {
        top: this.top + 'px',
        left: this.left + 'px',
        width: this.width + 'px',
        height: this.height + 'px',
        zIndex: this.zIndex,
        transform: `rotate(${this.rotate}deg)`
      }
    }
  },
  methods: {
    
  },
  created: function () {
    console.log('created', this)
  },
  mounted: function () {
    console.log('mounted', this)
  },
  updated: function () {
    console.log('updated', this)
  }
}
</script>