<template>
  <div class="container">
    <Inputs />
    <Display />
  </div>
</template>

<script>
import Inputs from "@/components/Inputs.vue";
import Display from "@/components/Display.vue";

export default {
  name: "App",
  components: {
    Inputs,
    Display,
  },
  data() {
    return {
      cardinalDirections: ["N", "E", "S", "W"],
      validOrders: ["A", "L", "R"],
      square: {
        maxX: null,
        maxY: null,
      },
      rover: {
        x: 0,
        y: 0,
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
          const indexOfCurrentOrientation = cardinalDirections.findIndex(
            (d) => d === rover.orientation
          );
          if (direction === "L" && indexOfCurrentOrientation === 0) {
            this.orientation = cardinalDirections[3];
            return;
          }
          if (direction === "L" && indexOfCurrentOrientation > 0) {
            this.orientation =
              cardinalDirections[indexOfCurrentOrientation - 1];
            return;
          }
          if (direction === "R" && indexOfCurrentOrientation === 3) {
            this.orientation = cardinalDirections[0];
            return;
          }
          if (direction === "R" && indexOfCurrentOrientation < 3) {
            this.orientation =
              cardinalDirections[indexOfCurrentOrientation + 1];
            return;
          }
        },
        tweet() {
          console.log(
            `Rover says: "Hello, Earth! I'm in Mars, at x=${this.x}, y=${this.y}, orientated to ${this.orientation}"
            `
          );
        },
      },
    };
  },
  methods: {
    position(initialPosition) {
      if (
        typeof initialPosition.x !== "number" ||
        typeof initialPosition.y !== "number"
      ) {
        console.error("Type error: Enter X and Y coordiates as numbers");
        return;
      }
      if (
        initialPosition.x > square.MaxX ||
        initialPosition.x < 0 ||
        initialPosition.y > square.MaxY ||
        initialPosition.y < 0
      ) {
        console.error("Error: Rover must be positioned inside the square");
        return;
      }
      rover.x = initialPosition.x;
      rover.y = initialPosition.y;
      console.log(
        ` Rover initial coordinates: x = ${initialPosition.x}, y = ${initialPosition.y}`
      );
      return;
    },
    orientate(initialOrientation) {
      if (typeof initialOrientation !== "string") {
        console.error(
          "Wrong input type, please enter a valid string (N, E, S or W)"
        );
        return;
      }
      const input = initialOrientation.toUpperCase();
      let orientationIndex = cardinalDirections.findIndex(
        (direction) => direction === input
      );
      if (orientationIndex === -1) {
        console.error(
          "Initial orientation must be a cardinal direction: N, E, S or W"
        );
        return;
      }
      rover.orientation = cardinalDirections[orientationIndex];
      console.log(
        `Initial orientation set to ${cardinalDirections[orientationIndex]} `
      );
      return;
    },
    moveRover(sequence) {
      if (typeof sequence !== "string") {
        console.error(
          "Wrong input type, please enter a valid string (A, L or R)"
        );
        return;
      }
      let inputIsValid = true;
      const input = sequence.toUpperCase().split("");
      input.forEach((order) => {
        if (!validOrders.includes(order)) {
          console.error(
            `Your sequence ${sequence} includes incorrect orders. Valid orders are only: A (advance), L (turn left), R(turn right) `
          );
          inputIsValid = false;
          return;
        }
      });
      if (inputIsValid) {
        console.log(`...Validating sequence ${sequence}... `);
        for (let i = 0; i < input.length; i++) {
          // Using a traditional for loop because so we can use break (break is not allowed in forEach())
          if (input[i] === "A") rover.advance();
          if (input[i] === "L" || "R") rover.turn(input[i]);
          checkPosition(i);
          if (rover.positionIsValid === false) {
            position(inputPosition);
            orientate(inputOrientation);
            break;
          }
        }
      }
      console.log(rover.positionIsValid);
    },
    checkPosition(position) {
      if (
        rover.positionIsValid &&
        (rover.x < 0 ||
          rover.x > square.MaxX ||
          rover.y < 0 ||
          rover.y > square.MaxY)
      ) {
        console.error(
          `Sequence could not be executed, order #${
            position + 1
          } would put the rover out of the square`
        );
        rover.positionIsValid = false;
        return;
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.container {
  display: flex;
}
</style>

position(inputPosition); orientate(inputOrientation); rover.tweet();
moveRover("RRAAAA"); rover.tweet(); };
