Command &amp; Conquer - HTML5
=============================

## About

This is a recreation of the original Command and Conquer: Tiberian Dawn, Real Time Strategy game entirely in HTML5 and JavaScript.

This project is only intended as a technical proof of concept to demonstrate the basic working elements of an RTS game in HTML5. No commercial use is intended. All images and sounds used are from C&C - Tiberian Dawn and are property of the original game creators.

This game works best on Google Chrome or Mozilla Firefox. The images can take a little while to load so please be patient.

## Documentation

This repository now includes comprehensive documentation to help understand the implementation:

- [DOCUMENTATION.md](DOCUMENTATION.md) - General project overview and architecture
- [TECHNICAL_DOCUMENTATION.md](TECHNICAL_DOCUMENTATION.md) - In-depth technical analysis of core systems

## Notes & Demo URL

You can find a working demo of this project on http://www.adityaravishankar.com/projects/games/command-and-conquer-demo/

Details and notes about the development of the project are available on my website at http://www.adityaravishankar.com/2011/11/command-and-conquer-programming-an-rts-game-in-html5-and-javascript/

NOTE: The source code shared here is from the earlier demo version of the project. This version is no longer being developed and the code is being shared so others can learn from it.

## Newer Version & Updates

A more recent version of the project is available here. http://www.adityaravishankar.com/projects/games/command-and-conquer/

This version is a complete rewrite of the earlier demo shared on github.

The new version has more levels from the original game, more units, explosions, effects and background music. Multiplayer support is also being tested using Node.js & nowjs. 

[Demo Video](http://www.youtube.com/watch?v=HTZCMxNtloQ)

This new version is NOT open source. It is still free to play. 

News, updates, screenshots, videos and invites to beta releases are available on the [C&C HTML5 Facebook page](http://www.facebook.com/CommandConquerHtml5)

## Getting Started

To run the game locally:

1. Clone or download this repository
2. Open `index.html` in a modern web browser (Chrome or Firefox recommended)
3. Wait for assets to load (this may take a moment)
4. Use the controls described in the in-game help to play

## Controls

- **Unit Selection**: Click on a unit to select it
- **Drag Selection**: Drag mouse to select multiple units
- **Multiple Selection**: Shift + Click to add units to selection
- **Movement**: Select unit(s) and click on destination to move
- **Attack**: Select unit(s) and click on target building or unit to attack
- **Map Panning**: Move cursor near edge of map to pan and scroll around
- **Building**: Move MCV to desired location and click to deploy
- **Construction**: Expand sidebar and click on construction options

## Technical Features

- HTML5 Canvas for rendering
- A* pathfinding for unit movement
- Real-time strategy game mechanics
- Fog of war implementation
- Sprite-based animation system
- Sound effects and voice prompts
- Resource management (Tiberium harvesting)
- Building construction and destruction
- Unit AI with patrol, guard, and attack behaviors