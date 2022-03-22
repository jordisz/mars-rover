<template>
  <div class="container">
    <Inputs
      @setSquare="setSquare"
      @setPosition="setPosition"
      @runSequence="runSequence"
      @restartRover="restartRover"
      :sequenceIsCorrect="sequenceIsCorrect"
    />
    <Display
      :areaHeight="square.maxY"
      :areaWidth="square.maxX"
      :roverPositionX="rover.x"
      :roverPositionY="rover.y"
      :roverOrientation="rover.orientation"
      :sequenceIsCorrect="sequenceIsCorrect"
      :showRover="showRover"
      :message="message"
    />
  </div>
</template>

<script>
import "@fontsource/titillium-web/400.css";
import "@fontsource/titillium-web/600.css";
import Inputs from "@/components/Inputs.vue";
import Display from "@/components/Display.vue";

export default {
  name: "App",
  components: {
    Inputs,
    Display,
  },
  data() {
    let self = this;
    return {
      cardinalDirections: ["N", "E", "S", "W"],
      validOrders: ["A", "L", "R"],
      sequenceIsCorrect: null,
      square: {
        maxX: null,
        maxY: null,
      },
      startingPosition: {
        x: null,
        y: null,
      },
      startingOrientation: "",
      rover: {
        x: null,
        y: null,
        orientation: null,
        positionIsValid: true,
        advance() {
          switch (this.orientation) {
            case "N":
              this.y++;
              break;
            case "S":
              this.y--;
              break;
            case "E":
              this.x++;
              break;
            case "W":
              this.x--;
              break;
          }
        },
        turn(direction) {
          const indexOfCurrentOrientation = self.cardinalDirections.findIndex(
            (d) => d === this.orientation
          );
          if (direction === "L" && indexOfCurrentOrientation === 0) {
            this.orientation = self.cardinalDirections[3];
            return;
          }
          if (direction === "L" && indexOfCurrentOrientation > 0) {
            this.orientation =
              self.cardinalDirections[indexOfCurrentOrientation - 1];
            return;
          }
          if (direction === "R" && indexOfCurrentOrientation === 3) {
            this.orientation = self.cardinalDirections[0];
            return;
          }
          if (direction === "R" && indexOfCurrentOrientation < 3) {
            this.orientation =
              self.cardinalDirections[indexOfCurrentOrientation + 1];
            return;
          }
        },
        tweet() {
          self.message = {
            class: "success",
            text: `Rover says: "Hello, Earth! I'm in Mars, at x=${this.x}, y=${this.y}, orientated to ${this.orientation}"`,
          };
        },
      },
      showRover: false,
      message: {
        class: "blank",
        text: "",
      },
    };
  },
  methods: {
    setSquare(received) {
      this.square.maxX = received.maxX;
      this.square.maxY = received.maxY;
    },
    setPosition(receivedPosition, receivedOrientation) {
      this.position(receivedPosition);
      this.orientate(receivedOrientation);
    },
    runSequence(receivedSequence) {
      this.message = { class: "info", text: "" };
      this.moveRover(receivedSequence);
    },
    position(initialPosition) {
      this.rover.x = initialPosition.x;
      this.rover.y = initialPosition.y;
      this.startingPosition.x = initialPosition.x;
      this.startingPosition.y = initialPosition.y;
      return;
    },
    async orientate(initialOrientation) {
      const input = initialOrientation.toUpperCase();
      let orientationIndex = this.cardinalDirections.findIndex(
        (direction) => direction === input
      );
      this.rover.orientation = this.cardinalDirections[orientationIndex];
      this.startingOrientation = this.cardinalDirections[orientationIndex];
      await this.delay(900);
      if (this.sequenceIsCorrect !== false) this.rover.tweet();
      this.showRover = true;
      return;
    },
    async moveRover(sequence) {
      this.message = {
        class: "info",
        text: `...Validating sequence ${sequence}... `,
      };
      const input = sequence.toUpperCase().split("");
      for (let i = 0; i < input.length; i++) {
        // Using a traditional for loop because so we can use await and break (not allowed in forEach())
        await this.delay(500);
        if (input[i] === "A") this.rover.advance();
        if (input[i] === "L" || "R") this.rover.turn(input[i]);
        this.message = {
          class: "info",
          text: `...Validating sequence ${sequence}... (${input[i]}) `,
        };
        this.checkPosition(i);
        if (this.rover.positionIsValid === false) {
          this.position(this.startingPosition);
          this.orientate(this.startingOrientation);
          break;
        }
      }
      if (this.rover.positionIsValid) {
        this.sequenceIsCorrect = true;
        this.rover.tweet();
      }
    },
    checkPosition(position) {
      if (
        this.rover.positionIsValid &&
        (this.rover.x < 0 ||
          this.rover.x >= this.square.maxX ||
          this.rover.y < 0 ||
          this.rover.y >= this.square.maxY)
      ) {
        this.message = {
          class: "error",
          text: `Sequence could not be executed, order #${
            position + 1
          } would put the rover out of the square!`,
        };
        this.rover.positionIsValid = false;
        this.sequenceIsCorrect = false;
        return;
      }
    },
    delay(time) {
      return new Promise((resolve) => setTimeout(resolve, time));
    },
    restartRover() {
      this.sequenceIsCorrect = null;
      this.rover.positionIsValid = true;
      this.rover.tweet();
    },
    reset() {
      this.sequenceIsCorrect = null;
      this.square.maxX = null;
      this.square.maxY = null;
      this.startingPosition.x = null;
      this.startingPosition.y = null;
      this.startingOrientation = null;
      this.rover.x = null;
      this.rover.y = null;
      this.rover.orientation = null;
      this.rover.positionIsValid = true;
      this.message = {
        class: "blank",
        text: "",
      };
    },
  },
};
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: "Titillium Web", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.container {
  display: flex;
}
</style>
