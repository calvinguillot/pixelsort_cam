<template>
  <v-container fluid class="pa-0 ma-0">
    <div id="p5canvas" style="height:100vh"></div>
    <v-row class="panelPOS">
      <v-col cols="12">
        <v-row justify="center" align="center">
          <v-col cols="12">
            <v-slider
              v-model="gain"
              color="white"
              track-color="grey lighten-1"
              min="0"
              max="255"
              vertical
            ></v-slider>
          </v-col>
          <v-col v-if="isMobile" cols="12" class="text-center">
            <v-btn small fab elevation="10" @click="switchCamera">
              <v-icon>mdi-camera-switch</v-icon>
            </v-btn>
          </v-col>
          <!-- <v-col cols="12" class="text-center">
            <v-btn small fab elevation="10" @click="saveImage">
              <v-icon>mdi-download</v-icon>
            </v-btn>
          </v-col>-->
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import p5 from "p5";

export default {
  data() {
    return {
      gain: 75,
      resolution: 8,
      ratio: 1.333,
      cameraSel: 0,
      isMobile: false,
      cameraTypes: ["user", { exact: "environment" }],
      // saved: false,
    };
  },
  methods: {
    psort() {
      const s = (p5) => {
        var capture, step, pxsV, wW, wH, rW, transV, translation, scaling;
        let unsorted = new Array();
        p5.setup = () => {
          let constraints = {
            audio: false,
            video: {
              facingMode: this.cameraTypes[this.cameraSel],
            },
          };
          wW = window.innerWidth;
          wH = window.innerHeight;
          rW = p5.floor(wH * this.ratio);
          p5.createCanvas(wW, wH);
          capture = p5.createCapture(constraints, p5.VIDEO);
          capture.size(rW, wH);
          capture.hide();
          p5.noStroke();

          if (wW > wH) {
            transV = (wW - rW) / 2;
          } else {
            transV = ((rW - wW) / 2) * -1;
          }

          if (this.cameraSel === 0) {
            scaling = -1;
            translation = transV + rW;
          } else {
            scaling = 1;
            translation = transV;
          }
        };
        p5.draw = () => {
          step = this.resolution;
          pxsV = this.gain;
          p5.translate(translation, 0);
          p5.scale(scaling, 1);
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

          for (let i = 0; i < unsorted.length; i++) {
            unsorted[i].sort(function (a, b) {
              if (a.l < pxsV) return b.l - a.l;
            });
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
          // if (this.saved === true) {
          //   this.saved = getImage();
          // }
        };
        // function getImage() {
        //   p5.saveCanvas(img, "pixelsort_camera_cg", "png");
        //   return false;
        // }
      };
      new p5(s, "p5canvas");
    },
    removePrev() {
      let divs = document.getElementsByTagName("canvas");
      if (divs.length > 0) {
        divs[0].parentNode.removeChild(divs[0]);
      }
    },
    switchCamera() {
      if (this.cameraSel === 0) {
        this.cameraSel = 1;
        this.removePrev();
        this.psort();
      } else {
        this.cameraSel = 0;
        this.removePrev();
        this.psort();
      }
    },
    // saveImage() {
    //   this.saved = true;
    // },
  },
  mounted() {
    if (window.innerWidth < window.innerHeight) {
      this.ratio = 0.777;
      this.isMobile = true;
    } else {
      this.ratio = 1.333;
      this.isMobile = false;
    }
    this.psort();
  },
};
</script>

<style scoped>
.panelPOS {
  position: absolute;
  top: 0;
  left: 0px;
  width: 100px;
  z-index: 10;
}
</style>