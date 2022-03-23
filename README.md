# mars-rover

Solution for a tech interview coding challenge.
It simulates a GUI for sending instructions to move a rover on Mars, validating the movements so the rover doesn't exit a predefined area.

## Instructions

Just clone the repo and then

```
npm install
```

Postinstall script will automatically serve the app and open it in your browser.

## Technologies used

As required by the challenge, the app is made with Vue / vanilla JS.
It also uses GSAP to simplify the animation syntax, and Fontsource to import the typographic font.

## App operation

A rover is placed in a square inside a bigger area.
The user defines the width and height in squares of the area.
The coordinates of the bottom left corner are (0,0).

Then the user defines the coordinates for the initial placement of the rover, and also its initial orientation.
After the rover is in its initial position, the user can enter a series of commands (A for Advance, L and R for turn Left or Right).
There is no fixed limit of number of input commands.

The app validates if all the commands can be executed without exceding the area limits.
If there is a command that places the rover outside of the area, the app shows an error message.
If the entire sequence of commands can be executed, the rover moves and sends a message with its final position and orientation.
