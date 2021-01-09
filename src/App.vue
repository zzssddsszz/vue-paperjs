<template>
  <div id="app">
    <canvas id="myCanvas" class="myCanvas"></canvas>
    <form>
      <label>체인길이</label>
      <input v-model="chain.length" type="text"/>
    </form>
  </div>

</template>

<script>
import paper from "paper"

export default {
  name: 'App',
  data: () => ({
    path: null,
    scope: null,
    myCanvas: null,
    paths: null,
    chain: {
      length: null
    }
  }),
  methods: {
    pathCreate(scope, chainLength, ...value) {
      // scope.activate();
      let path = new paper.Path({
        strokeColor: "#000000",
        strokeJoin: 'round',
        strokeWidth: 1.5
      });

      let head = new paper.Point(0,0);
      let to;
      let target;
      if (value != null && typeof value == "object" && !Object.keys(value).length) {
        console.log("value는 null입니다.");
        target= new paper.Point(this.canvasWidth / 2, this.canvasHeight);
      }

      //무한루프 몇번도나
      let co = 0;
      //좌측상단에서 중간하단 찍고 우측상단까지 패스
      while (head.getDistance(new paper.Point(this.canvasWidth, 0)) > chainLength) {
        if (head.getDistance(target) < chainLength) {
          target = new paper.Point(this.canvasWidth, 0);
        }
        let angle = target.subtract(head).getAngle();

        let pointByAngle = new paper.Point({
          angle: angle,
          length: chainLength
        });

        to = head.add(pointByAngle);

        path.addSegments([head, to]);
        head = to;

        co++;
        if (co > 300) {
          //무한루프 안전코드
          break;
        }
      }
      console.log(co + "번 반복함");

      //중간체인을 잘보이는 가운데로 맞추기
      let index = parseInt(path.segments.length / 2);
      path.segments[index - 1].point.x = this.canvasWidth / 2 - chainLength / 2;
      path.segments[index - 1].point.y = this.canvasHeight * 0.78;
      path.segments[index].point.x = path.segments[index - 1].point.x + chainLength;
      path.segments[index].point.y = path.segments[index - 1].point.y;


      //초기각도는 중간이기때문에 0
      let angle = 0;
      head = path.segments[index].point;
      //자연스럽게 하기 위해 캔버스 각도 조절
      target = new paper.Point(this.canvasWidth*1.4 , -this.canvasHeight*2);
      //중앙부터 우측 상단까지의 체인 조절
      for (let i = path.segments.length / 2 ,  z = 0 ; i < path.segments.length; i++,z++) {
        angle = angle%360;
        angle += (target.subtract(path.segments[i-1].point).getAngle()- angle)*(z/(path.segments.length / 2))*1.5;

        console.log(angle)
        let vector = new paper.Point({
          angle:angle,
          length:chainLength
        })
        path.segments[i].point.set({
          x:path.segments[i-1].point.x+ vector.x,
          y:path.segments[i-1].point.y+ vector.y
        })
      }

      //중앙부터 좌측 상단까지의 체인 조절
      angle = 180;
      target = new paper.Point(-this.canvasWidth*1.4 , -this.canvasHeight*2);
      for (let i = path.segments.length / 2-1 ,  z = 0 ; i > 0; i--,z++) {
        angle = angle%360;
        angle += (target.subtract(path.segments[i].point).getAngle()- angle)*(z/(path.segments.length / 2))*1.5;

        console.log(angle)
        let vector = new paper.Point({
          angle:angle,
          length:chainLength
        })
        path.segments[i-1].point.set({
          x:path.segments[i].point.x+ vector.x,
          y:path.segments[i].point.y+ vector.y
        })
      }


      new paper.Path.Circle({
        center: target,
        radius: 10,
        strokeColor: 'black'
      });
      path.fullySelected = true;




      return path;
    },
    buttonClick() {
      let self = this;
      self.pathCreate(self.scope, 10);


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
.myCanvas {
  width: 500px;
  height: 500px;
  display: block;
  border: 3px solid black;
}
</style>
