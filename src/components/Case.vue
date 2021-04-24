<template>
  <div class="case" :style="style">
  </div>
</template>

<script>
export default {
  name: 'Case',
  props: {
    width : Number,
    height : Number,
  },
  computed:{
    style(){
      return `width:${this.$props.width}px;height:${this.$props.height}px;`
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.case{
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
  margin: 0px;
  padding: 0px;
  border: solid 0.2px grey;
  background-color:transparent;
  box-sizing: border-box;
  -moz-box-sizing: border-box;
  -webkit-box-sizing: border-box;
}

</style>
