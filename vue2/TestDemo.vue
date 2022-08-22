<template>
  <div style="width: 100%;">
<!--    <draggable-container @success="onSuccess" class="border-red" >-->
<!--      <div style="height: 250px; background-color: #747bff">-->
<!--        oooo-->
<!--      </div>-->
<!--    </draggable-container>-->
    <div style="height: 100px;">
      white space
    </div>
    <draggable-container v-model:offset="offset" height="300" max-height="400" min-height="200" @success="onSuccess"  class="border-red">
      <div style="height: 250px; background-color: #747bff">
        hhhh
      </div>
    </draggable-container>
  </div>
</template>

<script>
import DraggableContainer from "./DraggableContainer.vue";

export default {
  name: "TestDemo",
  components: {
    DraggableContainer
  },
  data() {
    return {
      adjustHeight: 0,
      offset: 0,
    }
  },
  watch: {
    offset(val) {
      console.log(val)
    }
  },
  methods: {
   onSuccess: function (offsetY, height) {
     console.log('offsetY', offsetY, height)
     this.adjustHeight = offsetY
   }
  }
}
</script>

<style scoped>
.border-red {
  border: 1px solid red;
}
</style>
