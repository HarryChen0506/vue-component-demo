<style>
  .active {
    border: 1px dashed black;
  }
  h2 {
    opacity: 0.3;
    padding: 0 0 0 20px;
    z-index: -1;
  }
  .dragHandle {
    position: absolute;
    top: 0;
    right: 0;
    padding: 5px;
    font-size: 20px;
    background-color: lightgrey;
  }
  .vdr {
    background: white;
  }
</style>
<template>
  <div class="about">
    <h1>This is an about page</h1>
    <div style="height: 500px; width: 500px; border: 1px solid red; position: relative;">
      <vue-draggable-resizable :w="100" :h="100" v-on:dragging="onDrag" v-on:resizing="onResize" :parent="true" >
        <p>Hello! <br>
        X: {{ x }} / Y: {{ y }} - Width: {{ width }} / Height: {{ height }}</p>
      </vue-draggable-resizable>
    </div>
  </div>
</template>
<script>

import VueDraggableResizable from 'vue-draggable-resizable'
export default {
  components: {
    VueDraggableResizable
  },
  data: function () {
    return {
      width: 0,
      height: 0,
      x: 0,
      y: 0
    }
  },
  methods: {
    onResize: function (x, y, width, height) {
      this.x = x
      this.y = y
      this.width = width
      this.height = height
      console.log('onResize', x, y)
    },
    onDrag: function (x, y) {
      this.x = x
      this.y = y
    }
  }
}
</script>


