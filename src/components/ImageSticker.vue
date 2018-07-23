<style scoped>
.image-sticker {
  position: absolute;
  box-sizing: border-box;
  border: 1px solid #333
}
</style>

<template>
  <div 
    class="image-sticker" 
    :style="style" 
    @touchstart="onStickerTouchstart"
    @touchmove="onStickerTouchmove"
    @touchend="onStickerTouchend" >
  </div>
</template>
<script>
const gesture = {
  startX: 0,
  startY: 0,
  zoom: false,
  distance: 0,
  preV: {x:null, y:null},
  center: {x:0, y:0}, // 中心点y坐标
  scale: 1
}
// 函数截留
const throttle = function (func, deltaX) {
  let lastCalledAt = new Date().getTime();
  // let that = this;  
  return function() {    
    // func.apply(that, arguments);
    // console.log('new Date().getTime()', new Date().getTime())
    // console.log('lastCalledAt', lastCalledAt)
    // console.log('deltaX', deltaX)
    // console.log('throttle', this)
    if(new Date().getTime() - lastCalledAt >= deltaX) {
        func.apply(this, arguments);
        lastCalledAt = new Date().getTime();
        // console.log('执行')
    } else {
      console.log('不执行')
    }
  }
}
// 角度计算
const getLen = function(v) {
  return Math.sqrt(v.x * v.x + v.y * v.y);
}
const dot = function (v1, v2) {
  return v1.x * v2.x + v1.y * v2.y;
}
const getAngle = function (v1, v2) {
  let mr = getLen(v1) * getLen(v2);
  if (mr === 0) return 0;
  let r = dot(v1, v2) / mr;
  if (r > 1) r = 1;
  return Math.acos(r);
}
const cross = function (v1, v2) {
  return v1.x * v2.y - v2.x * v1.y;
}
const getRotateAngle = function (v1, v2) {
  let angle = getAngle(v1, v2);
  if (cross(v1, v2) > 0) {
    angle *= -1;
  }
  return angle * 180 / Math.PI;
}
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
      width: this.state.width || 0,
      height: this.state.height || 0,
      originWidth: this.state.width || 0,
      originHeight: this.state.height || 0,
      left: this.state.left || 0,
      top: this.state.top || 0,
      rotate: this.state.rotate || 0,
      zIndex: this.state.zIndex || 0,
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
  watch: {
    state:  function (val, oldVal) {
      console.log('watch state', val, oldVal)
      const {top, left, width, height, zIndex, rotate} = val
      this.top = top
      this.left = left
      this.width = width
      this.height = height
      this.originWidth = width|| 0,
      this.originHeight = height || 0,
      this.zIndex = zIndex
      this.rotate = rotate
    }
  },
  methods: {
    onStickerTouchstart: function (e) {
      console.log('onStickerTouchstart', this)
      const parentOffsetX = this.parent.left
      const parentOffsetY = this.parent.top
      if (e.touches.length === 1) {
        let { clientX, clientY } = e.touches[0]
        gesture.startX = clientX - parentOffsetX
        gesture.startY = clientY - parentOffsetY     
        console.log('gesture-one', gesture);
      } else {        
        let xMove = e.touches[1].clientX - e.touches[0].clientX
        let yMove = e.touches[1].clientY - e.touches[0].clientY
        let distance = Math.sqrt(xMove * xMove + yMove * yMove)
        // 记录旋转
        let v = { x: xMove, y: yMove }
        gesture.preV = v
        gesture.distance = distance
        gesture.zoom = true
        console.log('双指缩放', this.gesture)
      }
    },
    onStickerTouchmove: throttle(function (e) {
      this.stickerTouchmove(e)
    }, 1000/60),   
    stickerTouchmove (e) {
      // console.log('stickerOntouchmove', e)
      const parentOffsetX = this.parent.left || 0
      const parentOffsetY = this.parent.top || 0
      if (e.touches.length === 1) {
        //单指移动
        if (gesture.zoom) {
          //缩放状态，不处理单指
          return
        }
        let { clientX, clientY } = e.touches[0];
        const pointX = clientX - parentOffsetX // 触摸点所在画框坐标系的x坐标
        const pointY = clientY - parentOffsetY // 触摸点所在画框坐标系的y坐标
        let offsetX = pointX - gesture.startX;
        let offsetY = pointY - gesture.startY;
        gesture.startX = pointX;
        gesture.startY = pointY;
        // console.log('gesture', gesture)
        this.left += offsetX
        this.top += offsetY
      } else {
        //双指缩放
        let xMove = e.touches[1].clientX - e.touches[0].clientX;
        let yMove = e.touches[1].clientY - e.touches[0].clientY;
        let distance = Math.sqrt(xMove * xMove + yMove * yMove);
        
        // 计算缩放
        let distanceDiff = distance - gesture.distance;
        let newScale = gesture.scale + 0.005 * distanceDiff;
        // console.log('newScale', newScale)
        if (newScale < 0.3) {
          newScale = 0.3;
        }
        if (newScale > 4) {
          newScale = 4;
        }
        let newWidth = newScale * this.originWidth
        let newHeight = newScale * this.originHeight
        let newX = this.left - (newWidth - gesture.scale * this.originWidth) * 0.5
        let newY = this.top - (newHeight - gesture.scale * this.originHeight) * 0.5

        // // 计算旋转
        let newRotate
        let preV = gesture.preV
        let v = {
          x: xMove,
          y: yMove
        }
        if (preV.x !== null) {
          let angle = getRotateAngle(v, preV)          
          newRotate = parseFloat(this.rotate) + angle
        }
        // 更新数据
        gesture.scale = newScale
        gesture.distance = distance
        gesture.preV = v
        this.width = newWidth
        this.height = newHeight        
        this.left = newX
        this.top = newY
        this.rotate = newRotate      
      }
    },
    // 贴纸touchEnd
    onStickerTouchend (e) {
      // console.log('stickerOntouchend', e)
      if (e.touches.length === 0) {
        //重置缩放状态
        gesture.zoom = false
      }
    }
  },
  created: function () {
    console.log('created', this)
    this.parent = {} // 父容器属性
    
  },
  mounted: function () {
    console.log('mounted', this)    
    this.parent = this.$el.parentNode.getBoundingClientRect()
    console.log('parent', this.parent) 
  },
  updated: function () {
    // console.log('updated', this)
  }  
}
</script>