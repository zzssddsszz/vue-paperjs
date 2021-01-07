<template>
  <div id="app">
    <canvas v-model="myCanvas" class="myCanvas"></canvas>
    <button v-on:click="buttonClick">클릭</button>
  </div>

</template>

<script>
import paper from "paper"

export default {
  name: 'App',
  props:['myCanvas'],
  data:()=>({
    path: null,
    scope: null,
    myCanvas:null
  }),
  methods:{
    pathCreate(scope) {
      scope.activate();
      return new paper.Path({
        strokeColor:"#000000",
        strokeJoin: 'round',
        strokeWidth: 1.5
      })
    },
    buttonClick(){
      let self = this;
      self.path = self.pathCreate(self.scope);
      self.path.add(0, 0);
      self.path.add(this.canvasWidth/2, this.canvasHeight);

    }
  },
  mounted() {
    this.scope = new paper.PaperScope();
    this.scope.setup(this.myCanvas);
    console.log(this.myCanvas);
    // this.canvasHeight = this.myCanvas.height;
    // this.canvasWidth = this.myCanvas.width;
  }
}
</script>

<style>
.myCanvas{
  width: 500px;
  height: 500px;
  display: block;
  border: 3px solid black;
}
</style>
