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
    pathCreate(scope,sPoint,ePoint,chainLength,...value) {
      scope.activate();
      let path = new paper.Path({
        strokeColor:"#000000",
        strokeJoin: 'round',
        strokeWidth: 1.5
      });

      // let count = new paper.Point(0,0)

      if (value == null){
        console.log("value는 null입니다.");
      }

      let head = sPoint;
      // let vector = new paper.Point(5,5);
      let to;
      let target = new paper.Point(this.canvasWidth/2,this.canvasHeight);
      let co = 0;
      while (head.getDistance(new paper.Point(this.canvasWidth,0)) > chainLength){
        if (head.getDistance(target) < chainLength){
          target = new paper.Point(this.canvasWidth, 0);
        }
        let angle = target.subtract(head).getAngle();
        // console.log(angle);
        // angle = 90.0;
        // // let weight = 0.0;
        // if (angle > 0) {
        //   angle += -target.subtract(head).getAngle()*0.1;
        // }else {
        //   // angle += 10.0;
        // }

        let pointByAngle = new paper.Point({
          angle:angle,
          length:chainLength
        });

        to = head.add(pointByAngle);

        path.addSegments([head,to]);
        head = to;

        co++;
        if (co > 300) {
          break;
        }
      }
      console.log(co + "번 반복함");


      path.segments.forEach((segment)=>{
        if (segment.index < path.segments.length/2) {
          segment.point.angle += segment.index*0.1;
          console.log(segment);
        }

      })




      new paper.Path.Circle({
        center:target,
        radius:10,
        strokeColor:'black'
      });
      path.fullySelected = true;


      return path;
    },
    buttonClick(){
      let self = this;
      self.pathCreate(self.scope,new paper.Point(0,0),new paper.Point(this.canvasWidth),10,null);


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
