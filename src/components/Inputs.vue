<template>
  <div class="inputs-container">
    <div class="title-container">
      <h1>Martian Steps</h1>
      <h2>
        <span class="arrow"><img src="@/assets/arrow.svg" alt="" /></span>Moving
        a vehicle on Mars<span class="arrow reverse"
          ><img src="@/assets/arrow.svg" alt=""
        /></span>
      </h2>
    </div>
    <!-- AREA INPUT -->
    <div class="inputs-group">
      <h3 class="inputs-group-header" v-if="step === 0">DEFINE AREA</h3>
      <h3 class="inputs-group-header" v-else>AREA</h3>
      <div class="inputs-column" v-if="step === 0">
        <p>For optimal experience, use values between 4 and 40</p>
        <label for="">Enter area width:</label>
        <input
          class="input-numeric"
          type="number"
          min="1"
          v-model.number="square.maxX"
          @keydown="numberKeydown($event)"
        />
        <label>Enter area height:</label>
        <input
          class="input-numeric"
          type="number"
          min="1"
          v-model.number="square.maxY"
          @keydown="numberKeydown($event)"
        />
      </div>
      <div v-else>
        <p>Area: {{ square.maxX }} x {{ square.maxY }}</p>
      </div>
      <button
        v-show="
          square.maxX > 0 &&
          square.maxX !== '' &&
          square.maxY > 0 &&
          square.maxY !== '' &&
          step === 0
        "
        @click="sendSquare"
      >
        Done
      </button>
    </div>

    <!-- POSITION INPUT -->
    <div class="inputs-group" v-if="step > 0">
      <h3 class="inputs-group-header" v-if="step === 1">
        DEFINE INITIAL ROVER POSITION
      </h3>
      <h3 class="inputs-group-header" v-else>INITIAL ROVER POSITION</h3>
      <div class="inputs-column" v-if="step === 1">
        <p>
          Keep in mind that row and column numbering starts at 0, so the
          uppermost row is {{ square.maxY - 1 }} and the last column is
          {{ square.maxX - 1 }}
        </p>
        <label for="">Enter x (horizontal) position:</label>
        <input
          class="input-numeric"
          type="number"
          min="0"
          v-model.number="inputPosition.x"
          @keydown="numberKeydown($event)"
        />
        <label>Enter y (vertical) position:</label>
        <input
          class="input-numeric"
          type="number"
          min="0"
          v-model.number="inputPosition.y"
          @keydown="numberKeydown($event)"
        />
        <label>Enter initial rover orientation (N, E, S or W):</label>
        <input
          class="input-orientation"
          type="text"
          :value="inputOrientation"
          maxlength="1"
          @input="inputOrientation = $event.target.value.toUpperCase()"
          @keydown="orientationKeydown($event)"
        />
      </div>
      <div v-else>
        <p>Initial position: ({{ inputPosition.x }}, {{ inputPosition.y }})</p>
        <p>Initial orientation: {{ inputOrientation }}</p>
      </div>
      <button
        v-show="
          inputPosition.x < square.maxX &&
          inputPosition.x >= 0 &&
          inputPosition.x !== '' &&
          inputPosition.y < square.maxY &&
          inputPosition.y >= 0 &&
          inputPosition.x !== '' &&
          inputOrientation !== '' &&
          step === 1
        "
        @click="sendPosition"
      >
        Done
      </button>
    </div>

    <!-- SEQUENCE INPUT -->
    <div class="inputs-group" v-if="step > 1">
      <h3 class="inputs-group-header">ENTER SEQUENCE OF MOVEMENTS</h3>
      <label
        >Valid orders: A (move forward), L (turn left), R (turn right)</label
      >
      <input
        class="input-sequence"
        type="text"
        :value="sequence"
        @input="sequence = $event.target.value.toUpperCase()"
        @keydown="sequenceKeydown($event)"
      />
      <button
        v-if="
          sequence !== '' &&
          (sequenceIsCorrect === true || sequenceIsCorrect === null)
        "
        @click="sendSequence"
      >
        Run
      </button>
      <button
        v-if="sequenceIsCorrect === false"
        class="clear"
        @click="clearError"
      >
        Restart Rover
      </button>
    </div>
    <button class="reset" @click="reset" v-if="step > 0">Reset App</button>
  </div>
</template>

<script>
export default {
  name: "Inputs",
  props: {
    sequenceIsCorrect: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      step: 0,
      square: {
        maxX: null,
        maxY: null,
      },
      inputPosition: {
        x: null,
        y: null,
      },
      inputOrientation: "",
      sequence: "",
    };
  },
  methods: {
    numberKeydown(e) {
      if (/^\D$/.test(e.key)) {
        e.preventDefault();
      }
    },
    orientationKeydown(e) {
      if (
        !/^n|e|s|w$/i.test(e.key) &&
        e.keyCode !== 8 &&
        e.keyCode !== 16 &&
        e.keyCode !== 9
      ) {
        e.preventDefault();
      }
    },
    sequenceKeydown(e) {
      if (
        !/^a|l|r$/i.test(e.key) &&
        e.keyCode !== 8 &&
        e.keyCode !== 16 &&
        e.keyCode !== 9
      ) {
        e.preventDefault();
      }
    },
    sendSquare() {
      this.$emit("setSquare", this.square);
      this.step = 1;
    },
    sendPosition() {
      this.$emit("setPosition", this.inputPosition, this.inputOrientation);
      this.step = 2;
    },
    sendSequence() {
      this.$emit("runSequence", this.sequence);
      this.step = 3;
    },
    clearError() {
      this.$emit("restartRover");
      this.sequence = "";
    },
    reset() {
      this.$emit("reset");
      this.step = 0;
      this.square.maxX = null;
      this.square.maxY = null;
      this.inputPosition.x = null;
      this.inputPosition.y = null;
      this.inputOrientation = "";
      this.sequence = "";
    },
  },
};
</script>

<style scoped>
h1 {
  margin: 0;
  text-align: center;
  text-transform: uppercase;
  font-size: 2.5rem;
}
h2 {
  margin: 0 0 1rem;
  text-align: center;
  font-size: 1.25rem;
}
.arrow {
  padding: 0 0.7rem;
  filter: invert(100%) sepia(0%) saturate(0%) hue-rotate(148deg)
    brightness(103%) contrast(102%);
}
.arrow img {
  height: 1rem;
  transform: rotate(90deg);
}
.reverse img {
  transform: rotate(270deg);
}
h3 {
  margin-bottom: 1rem;
}
p {
  margin: 0 0 1rem;
}
button {
  height: 1.6rem;
  margin-top: 0.6rem;
  cursor: pointer;
}
.inputs-container {
  height: calc(100vh - 64px);
  max-width: 460px;
  display: flex;
  flex-direction: column;
  padding: 2rem 2.5rem;
  gap: 2rem;
  background-color: #39719e;
  color: #fff;
}
.inputs-group,
.inputs-column {
  display: flex;
  flex-direction: column;
}
.inputs-group-header {
  font-weight: 600;
}
.input-numeric {
  width: 8ch;
}
.input-orientation {
  width: 2ch;
}
.input-sequence {
  width: calc(100% - 6px);
}
.reset,
.clear {
  background-color: #ff000080;
  border: 2px solid red;
  color: white;
}
.reset {
  margin-top: 4rem;
}
</style>
