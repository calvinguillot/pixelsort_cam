<template>
  <v-container fluid class="pa-0 ma-0">
    <div id="p5canvas" style="height:100vh"></div>
  </v-container>
</template>

<script>
import p5 from "p5";

export default {
  data() {
    return {
      sliderG: 75,
      sliderR: 20,
      ratio: 1.333,
    };
  },
  methods: {
    psort() {
      const s = (p5) => {
        var capture, step, pxsV, wW, wH, rW, transV;
        let unsorted = new Array();
        p5.setup = () => {
          p5.createCapture({
            audio: false,
            video: {
              facingMode: "user",
              // facingMode:  "environment"
            },
          });
          wW = window.innerWidth;
          wH = window.innerHeight;
          rW = p5.floor(wH * this.ratio);
          p5.createCanvas(wW, wH);
          capture = p5.createCapture(p5.VIDEO);
          capture.size(rW, wH);
          capture.hide();
          p5.noStroke();

          if (wW > wH) {
            transV = (wW - rW) / 2;
          } else {
            transV = ((rW - wW) / 2) * -1;
          }
        };
        p5.draw = () => {
          step = this.sliderR;
          pxsV = this.sliderG;
          p5.translate(transV + rW, 0);
          p5.scale(-1, 1);
          p5.background(0);
          capture.loadPixels();

          for (let x = 0; x < capture.width; x += step) {
            unsorted[x / step] = new Array();
            for (let y = 0; y < capture.height; y += step) {
              let i = (y * capture.width + x) * 4;
              let r = capture.pixels[i];
              let g = capture.pixels[i + 1];
              let b = capture.pixels[i + 2];
              let l = 0.299 * r + 0.587 * g + 0.114 * b;
              unsorted[x / step].push({
                l,
                x,
                y,
                step,
                r,
                g,
                b,
              });
            }
          }

          // let sortedArr = new Array();
          for (let i = 0; i < unsorted.length; i++) {
            // sortedArr.push(
            unsorted[i].sort(function (a, b) {
              if (a.l < pxsV) return b.l - a.l;
            });
            // );
          }

          for (let i = 0; i < unsorted.length; i++) {
            let yInc = 0;
            unsorted[i].forEach((px) => {
              px.y = yInc;
              yInc += step;
              p5.fill(px.r, px.g, px.b);
              p5.rect(px.x, px.y, px.step, px.step);
            });
          }
        };
      };
      new p5(s, "p5canvas");
    },
    removeFeed() {
      var list = document.getElementsByTagName("video"); 
      if (list.length > 0) {
        list[0].parentNode.removeChild(list[0]);
      }
    },
  },
  mounted() {
    if (window.innerWidth < window.innerHeight) {
      this.ratio = 0.777;
    } else {
      this.ratio = 1.333;
    }
    this.psort();
    setTimeout(this.removeFeed, 1000);
  },
};
</script>

<style scoped>
.sliderPos {
  position: absolute;
  transform: translate(-50%);
  bottom: 65px;
  left: 50%;
  z-index: 3;
  width: 200px;
}
</style>