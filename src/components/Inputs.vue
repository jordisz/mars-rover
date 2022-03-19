<template>
  <div class="inputs-container">
    <div>INPUTS</div>
    <div class="inputs-group">
      <div>DEFINE AREA</div>
      <label for="">Enter square width:</label>
      <input
        type="number"
        min="1"
        v-model="square.maxX"
        @keydown="numberKeydown($event)"
      />
      <label>Enter square height:</label>
      <input
        type="number"
        min="1"
        v-model="square.maxY"
        @keydown="numberKeydown($event)"
      />
      <button v-show="square.maxX > 0 && square.maxY > 0">Done</button>
    </div>
    <div class="inputs-group">
      <div>DEFINE INITIAL ROVER POSITION</div>
      <label for="">Enter x (horizontal) position:</label>
      <input
        type="number"
        min="0"
        v-model="inputPosition.x"
        @keydown="numberKeydown($event)"
      />
      <label>Enter y (vertical) position:</label>
      <input
        type="number"
        min="0"
        v-model="inputPosition.y"
        @keydown="numberKeydown($event)"
      />
      <label>Enter initial rover orientation (N, E, S or W)</label>
      <input
        type="text"
        :value="inputOrientation"
        maxlength="1"
        @input="inputOrientation = $event.target.value.toUpperCase()"
        @keydown="orientationKeydown($event)"
      />
      <button
        v-show="
          inputPosition.x <= square.maxX &&
          inputPosition.x >= 0 &&
          inputPosition.y <= square.maxY &&
          inputPosition.y >= 0 &&
          inputOrientation !== ''
        "
      >
        Done
      </button>
    </div>
    <div class="inputs-group">
      <div>ENTER SEQUENCE OF MOVEMENTS</div>
      <label
        >Valid orders: A (move forward), L (turn left), R (turn right)</label
      >
      <input
        type="text"
        :value="sequence"
        @input="sequence = $event.target.value.toUpperCase()"
        @keydown="sequenceKeydown($event)"
      />
      <button v-if="sequence !== ''">Run</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Inputs",
  data() {
    return {
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
        console.log(e);
        e.preventDefault();
      }
    },
  },
};
</script>

<style scoped>
.inputs-container {
  display: flex;
  flex-direction: column;
  gap: 3rem;
}
.inputs-group {
  display: flex;
  flex-direction: column;
}
</style>
