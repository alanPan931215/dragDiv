<template>
  <div :id="dragArea" class="draggable-container" >
    <slot></slot>
    <!-- targetZone, 点击该区域可拖动 -->
    <div class="target-zone" @mousedown.self="onDrag">
    <div class="expand-zone" @click.stop="expand" />
    </div>
  </div>
</template>

<script>
let cid = 1
export default {
  name: "DraggableContainer",
  props: {
    height: {
      default: 200,
      type: [Number, String],
    },
    minHeight: {
      default: 100,
      type: [Number, String],
    },
    maxHeight: {
      default: 300,
      type: [Number, String],
    },
  },
  computed:{
    dragArea() {
      return 'dragArea' + cid++
    }
  },
  data() {
    return {
      isExpand: false,
      containerHeight: this.height,
    }
  },
  mounted() {
    this.initHeight()
  },
  methods: {
    initHeight: function () {
      this.setHeight(this.height)
    },
    getTargetDiv() {
      return document.getElementById(this.dragArea);
    },
    setHeight: function (height) {
      this.getTargetDiv().style.height = `${height}px`;
    },
    getHeight: function () {
      return parseInt(this.getTargetDiv().style.height)
    },
    expand: function () {
      this.isExpand = !this.isExpand;
      this.setHeight(this.isExpand ? this.maxHeight : this.height);
      this.onSuccess()
    },
    onSuccess: function () {
      const newHeight = this.getHeight()
      this.$emit('success', newHeight - this.containerHeight, newHeight)
      this.containerHeight = newHeight
    },
    onDrag: function (e) {
      const targetDivHeight = this.getTargetDiv().offsetHeight;
      const startY = e.clientY;
      const maxHeight = this.maxHeight
      const minHeight = this.minHeight
      const _this = this
      const throttleMove = (e) =>  requestAnimationFrame(() => mousemove(e))
      function mousemove(e) {
        e.preventDefault();
        //取Y绝对值
        const distY = Math.abs(e.clientY - startY);
        if (e.clientY < startY) {
          _this.setHeight(targetDivHeight - distY)
        }
        if (e.clientY > startY) {
          _this.setHeight(targetDivHeight + distY)
        }
        const currentHeight = _this.getHeight()
        if (currentHeight >= maxHeight) {
          _this.setHeight(maxHeight)
        }
        if (currentHeight <= minHeight) {
          _this.setHeight(minHeight)
        }
      }
      document.onmousemove = throttleMove
      document.onmouseup = function () {
        _this.onSuccess()
        document.onmousemove = null;
        document.onmouseup = null;
      }
    }
  },
};
</script>

<style scoped>
.draggable-container {
  overflow: hidden;
  visibility: visible;
  width: 100%;
  position: relative;
}

.target-zone {
  margin-top: 10px;
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 10px;
  cursor: row-resize;
  z-index: 1;
}

.expand-zone {
  display: inline;
  background-color: #747bff;
  position: absolute;
  bottom: 0;
  width: 100px;
  height: 10px;
  left:50%;
  margin-left: -50px;
  cursor: pointer;
  z-index: 1;
}
</style>
