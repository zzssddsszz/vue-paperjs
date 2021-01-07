<template>
  <div id="app">
    <canvas id="myCanvas" class="myCanvas"></canvas>
    <form>
      <label>체인길이</label>
      <input v-model="chain.length" type="text" />
    </form>
  </div>

</template>

<script>
import paper from "paper"
import {Point} from  "paper"

export default {
  name: 'App',
  data:()=>({
    path: null,
    scope: null,
    myCanvas:null,
    paths:null,
    chain:{
      length:null
    }
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
      let rPath = self.pathCreate(self.scope);


      rPath.add(new Point(this.canvasWidth/2, this.canvasHeight*0.8));

      // let 근접
      // while ()



      rPath.fullySelected = true;

      this.path = rPath;

      // self.path.scale(0.5)
      console.log(self.path.segments);

      console.log(self.path);

    }
  },
  mounted() {
    this.myCanvas = document.getElementById("myCanvas");
    // paper.PaperScope.settings.hitTest()
    this.scope = new paper.PaperScope();
    this.scope.setup(this.myCanvas);
    // console.log(this.$el);
    this.canvasHeight = this.myCanvas.height;
    this.canvasWidth = this.myCanvas.width;

    // this.paths = this.path.segments();
    console.log(this.scope);
    // this.scope.interiorPoint();
    this.buttonClick();
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
