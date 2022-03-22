<template>
  <div
    class="display-area"
    v-if="displayAreaHeight && displayAreaWidth"
    :style="{
      'min-width': displayAreaWidth + 'px',
      'min-height': displayAreaHeight + 'px',
    }"
  >
    <div
      class="rover"
      :class="{
        round: roverWidth / roverHeight === 1,
        green: sequenceIsCorrect === true || sequenceIsCorrect === null,
        red: sequenceIsCorrect === false,
      }"
      :style="{
        width: roverWidth + 'px',
        height: roverHeight + 'px',
        position: 'absolute',
        top: displayAreaHeight - roverHeight + 'px',
      }"
    >
      <img
        class="arrow"
        src="@/assets/arrow.svg"
        alt=""
        :style="{ height: roverHeight / 2 + 'px' }"
      />
    </div>
    <div
      class="grid"
      :style="{
        'background-size': roverWidth + 'px ' + roverHeight + 'px',
        width: displayAreaWidth + 'px',
        height: displayAreaHeight + 'px',
      }"
    ></div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  name: "Display",
  props: {
    areaHeight: { type: Number },
    areaWidth: { type: Number },
    roverPositionX: { type: Number },
    roverPositionY: { type: Number },
    roverOrientation: { type: String },
    sequenceIsCorrect: { type: Boolean },
  },
  computed: {
    displayAreaWidth() {
      let quotient = parseInt(840 / this.areaWidth);
      let n = quotient * this.areaWidth;
      return n;
    },
    displayAreaHeight() {
      let quotient = parseInt(840 / this.areaHeight);
      let n = quotient * this.areaHeight;
      return n;
    },
    roverWidth() {
      return this.displayAreaWidth / this.areaWidth;
    },
    roverHeight() {
      return this.displayAreaHeight / this.areaHeight;
    },
  },
  watch: {
    roverPositionX: function () {
      gsap.to(".rover", {
        x: this.roverPositionX * this.roverWidth,
        duration: 1,
      });
    },
    roverPositionY: function () {
      gsap.to(".rover", {
        y: 0 - this.roverPositionY * this.roverHeight,
        duration: 1,
      });
    },
    roverOrientation: function () {
      if (this.roverOrientation === "N") {
        gsap.to(".arrow", { rotation: "0_short" });
      }
      if (this.roverOrientation === "E") {
        gsap.to(".arrow", { rotation: "90_short" });
      }
      if (this.roverOrientation === "S") {
        gsap.to(".arrow", { rotation: "180_short" });
      }
      if (this.roverOrientation === "W") {
        gsap.to(".arrow", { rotation: "270_short" });
      }
    },
  },
};
</script>

<style scoped>
.display-area {
  position: relative;
  padding: 0;
  border: 1px solid black;
  background-image: url("../assets/mars.jpg");
  margin: auto;
}
.grid {
  /* https://stackoverflow.com/questions/3540194/how-to-make-a-grid-like-graph-paper-grid-with-just-css */
  position: absolute;
  background-image: linear-gradient(to right, grey 1px, transparent 1px),
    linear-gradient(to bottom, grey 1px, transparent 1px);
}
.rover {
  box-sizing: border-box;
  background: transparent;
  border: 2px solid black;
}
.round {
  border-radius: 100%;
}
.green {
  filter: invert(24%) sepia(65%) saturate(7445%) hue-rotate(116deg)
    brightness(95%) contrast(106%);
}
.red {
  filter: invert(9%) sepia(98%) saturate(5657%) hue-rotate(8deg)
    brightness(100%) contrast(116%);
}
.arrow {
  display: block;
  position: relative;
  top: 25%;
  margin: auto;
}
</style>
