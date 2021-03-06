# Audio Visual

Feel free to clone this, modify it and play around. The important audio visualisation stuff is in `src/App.svelte`, in the `onClick` function, which has the animation loop that actually draws to the canvas. Some interesting settings to change might be `analyser.fftSize` (which has to be a power of two from 32 to 32768), and the colour details. This is currently set up to take from a microphone input, but it can be easily adapted to take from an `<audio/>` element or something similar.

## Setup

- `npm i`
- `npm run dev`
