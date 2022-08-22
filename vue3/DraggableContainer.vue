<template>
  <div ref="dragArea" class="draggable-container">
    <slot></slot>
    <!-- targetZone, 点击该区域可拖动 -->
    <div class="target-zone" @mousedown="onDrag" />
    <div class="expand-zone" @click="expand" />
  </div>
</template>

<script>
import { onMounted, ref } from "vue";
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
  setup(props, { emit }) {
    const dragArea = ref(null);
    let isExpand = false;
    const defaultHeight = props.height
    let height = defaultHeight;
    const getHeight = () => parseInt(dragArea.value.style.height)
    const setHeight = (height) =>  dragArea.value.style.height = `${height}px`
    onMounted(() => {
      setHeight(defaultHeight);
    });
    function throttle(fn, delay) {
      // 记录上一次函数触发的时间
      let lastTime = 0;
      return function () {
        // 记录当前函数触发的时间
        const nowTime = Date.now();
        if (nowTime - lastTime > delay) {
          // 修正this指向问题
          fn.call(this);
          // 同步时间
          lastTime = nowTime;
        }
      };
    }
    function onDrag(e) {
      const targetDivHeight = dragArea.value.offsetHeight;
      const startY = e.clientY;
      const maxHeight = props.maxHeight;
      const minHeight = props.minHeight;
      function mousemove(e) {
        e.preventDefault();
        //取Y绝对值
        const distY = Math.abs(e.clientY - startY);
        //往上方拖动：
        if (e.clientY < startY) {
          setHeight(targetDivHeight - distY);
        }
        //往下方拖动：
        if (e.clientY > startY) {
          setHeight(targetDivHeight + distY);
        }
        const currentHeight = parseInt(dragArea.value.style.height);
        if (currentHeight >= maxHeight) {
          setHeight(maxHeight);
        }
        if (currentHeight <= minHeight) {
          setHeight(minHeight);
        }
      }
      const throttleMove = (e) => throttle(() => mousemove(e), 200)()
      document.onmousemove = throttleMove
      document.onmouseup = function () {
        onSuccess()
        document.onmousemove = null;
        document.onmouseup = null;
      };
    }
    function onSuccess() {
      const newHeight = getHeight();
      const offset = newHeight - height
      emit("update:offset", offset);
      emit("success", offset, newHeight);
      height = newHeight;
    }
    function expand() {
      isExpand = !isExpand;
      setHeight(isExpand ? props.maxHeight : props.height);
      onSuccess()
    }
    return {
      dragArea,
      onDrag,
      expand,
    };
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
