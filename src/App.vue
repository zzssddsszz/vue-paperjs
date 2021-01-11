<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <canvas id="myCanvas" class="myCanvas"></canvas>
      </div>
      <div class="row">
        <form>
          <label>중앙팬던트 무게</label>
          <input v-model="chain.centerWeight" type="number"/>

          <button v-on:click="buttonClick">생성</button>
        </form>
      </div>
    </div>
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
      length: null,
      centerWeight: 10
    },
    model: null
  }),
  methods: {
    pathCreate(scope, chainLength, value) {
      function getPointByTarget(target, head, chainLength) {
        let angle = target.subtract(head).getAngle();

        let pointByAngle = new paper.Point({
          angle: angle,
          length: chainLength
        });
        return pointByAngle;
      }


      scope.activate();
      let path = new paper.Path({
        strokeColor: "#000000",
        strokeJoin: 'round',
        strokeWidth: 1.5
      });

      // console.log(path);

      let head = new paper.Point(0, 0);
      let to = null;
      let target = null;
      //중간 하단으로 목표점 설정
      target = new paper.Point(this.canvasWidth / 2, this.canvasHeight);

      if (value != null && typeof value == "object" && !Object.keys(value).length) {
        console.log("value 들어오면 작업 뭐할까")
      }

      //무한루프 몇번도나
      let co = 0;


//좌측상단에서 중간하단 찍고 우측상단까지 경로
      while (head.getDistance(new paper.Point(this.canvasWidth, 0)) > chainLength) {
        if (head.getDistance(target) < chainLength) {
          target = new paper.Point(this.canvasWidth, 0);
        }
        to = head.add(getPointByTarget(target, head, chainLength));
        //체인의 무게점
        // console.log(to);
        path.add(to);
        path.lastSegment.weight = 10;
        head = to;
        co++;
        if (co > 300) {
          //무한루프 안전코드
          break;
        }
      }

      //가운데 무게를 적용해봤음 뭔가 수정이 필요함
      path.segments[path.segments.length / 2].weight = parseInt(value.centerWeight);


      console.log(co + "번 반복함");
      // console.log(path.segments.length);

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
      target = new paper.Point(this.canvasWidth * 1.5, -this.canvasHeight * 2);
      let totalWeight = 0;
      for (let i = path.segments.length / 2; i < path.segments.length; i++) {
        totalWeight += path.segments[i].weight;
      }
      // console.log("총무게 : "+totalWeight);

      //중앙부터 우측 상단까지의 체인 조절
      for (let i = path.segments.length / 2, weight = 0; i < path.segments.length; i++) {
        if (weight / totalWeight > 0.5) {
          weight = totalWeight;
        } else {
          weight += path.segments[i].weight;
        }
        angle = angle % 360;
        if (!(i == path.segments.length / 2)) {
          angle += (target.subtract(path.segments[i - 1].point).getAngle() - angle) * (weight / 2 / totalWeight);
        }

        let vector = new paper.Point({
          angle: angle,
          length: chainLength
        })
        path.segments[i].point.set({
          x: path.segments[i - 1].point.x + vector.x,
          y: path.segments[i - 1].point.y + vector.y
        })
      }

      //중앙부터 좌측 상단까지의 체인 조절
      //초기각도는 좌측으로 -180도라서
      angle = -180;
      target = new paper.Point(-this.canvasWidth * 0.5, -this.canvasHeight * 2);

      totalWeight = 0;
      for (let i = path.segments.length / 2, z = 0; i > 0; i--, z++) {
        totalWeight += path.segments[i].weight;
      }
      // console.log(totalWeight);

      for (let i = path.segments.length / 2, weight = 0; i >= 0; i--) {
        if (weight / totalWeight > 0.5) {
          weight = totalWeight;
        } else {
          weight += path.segments[i + 1].weight;
        }


        angle = angle % 360;
        if (!(i == path.segments.length / 2)) {
          angle += (target.subtract(path.segments[i + 1].point).getAngle() - angle) * (weight / 2 / totalWeight);
        }
        let vector = new paper.Point({
          angle: angle,
          length: chainLength
        })

        path.segments[i].point.set({
          x: path.segments[i + 1].point.x + vector.x,
          y: path.segments[i + 1].point.y + vector.y
        })
      }


      /*new paper.Path.Circle({
        center: target,
        radius: 10,
        strokeColor: 'black'
      });*/
      path.fullySelected = true;


      return path;
    },
    buttonClick() {

      if (!this.scope.project.isEmpty()) {
        this.scope.project.clear();
      }
      let val = {
        centerWeight: this.chain.centerWeight
      }
      this.pathCreate(this.scope, 10, val);
      // this.scope.view.center.x = this.canvasWidth;
      // this.scope.view.center.y = this.canvasHeight;
      // this.scope.view.zoom = 0.5;
      console.log(this.scope)

    }
  },
  mounted() {
    this.myCanvas = document.getElementById("myCanvas");
    this.myCanvas.width = 500;
    this.myCanvas.height = 500;

    // paper.PaperScope.settings.hitTest()
    this.scope = new paper.PaperScope();
    this.scope.setup(this.myCanvas);
    // console.log(this.$el);
    this.canvasHeight = this.myCanvas.height;
    this.canvasWidth = this.myCanvas.width;

    // this.paths = this.path.segments();
    // this.scope.interiorPoint();
    this.buttonClick();
  }
}
</script>

<style>
.myCanvas {
  width: 500px;
  height: 500px;
  display: inline-block;
  border: 3px solid black;
}
</style>
