# Command & Conquer - HTML5 Documentation

## Table of Contents
1. [Overview](#overview)
2. [Project Structure](#project-structure)
3. [Core Components](#core-components)
4. [Game Architecture](#game-architecture)
5. [Main Game Loop](#main-game-loop)
6. [Rendering System](#rendering-system)
7. [Game Entities](#game-entities)
8. [Input Handling](#input-handling)
9. [Pathfinding](#pathfinding)
10. [Sound System](#sound-system)
11. [UI Components](#ui-components)

## Overview

This is a recreation of the original Command and Conquer: Tiberian Dawn, a Real Time Strategy (RTS) game implemented entirely in HTML5 and JavaScript. The project demonstrates the core mechanics of an RTS game using modern web technologies.

**Disclaimer**: This project is intended as a technical proof of concept and is not for commercial use. All images and sounds used are from C&C - Tiberian Dawn and are property of the original game creators.

## Project Structure

```
command-and-conquer/
├── audio/                 # Sound files
├── images/                # Game assets and sprites
├── js/                    # JavaScript source files
│   ├── cnc-v0.3d.js       # Main game implementation (current version)
│   ├── jquery.js          # jQuery library
│   └── ...                # Other JavaScript files (older versions)
├── index.html             # Main HTML file
├── debug.html             # Debug version
└── README.md              # Project information
```

## Core Components

### Main Files
- **index.html**: Entry point containing the HTML5 Canvas element and references to JavaScript files
- **js/cnc-v0.3d.js**: Main game implementation with all core logic
- **images/**: Contains all game sprites, maps, UI elements, and cursors

### Dependencies
- **jQuery**: Used for DOM manipulation and event handling
- **HTML5 Canvas**: For 2D graphics rendering
- **Web Audio API**: For sound playback

## Game Architecture

The game follows a modular architecture with several core systems:

### Global Objects
1. **c**: Mouse/ cursor handling system
2. **d**: Main game object containing core game state and functions
3. **e**: Sidebar UI system
4. **g**: Buildings system
5. **l**: Vehicles system
6. **j**: Infantry system
7. **m**: Turrets system
8. **o**: Overlay system (tiberium, trees, etc.)
9. **p**: Level system
10. **q**: Sound system
11. **A**: Fog of war system

### Game State Management
The game maintains state through various arrays:
- `d.units[]`: All active units
- `d.buildings[]`: All buildings
- `d.turrets[]`: All turrets
- `d.overlay[]`: Map overlays (tiberium, trees)
- `d.bullets[]`: Active projectiles

## Main Game Loop

The game uses a fixed interval game loop implemented with `setInterval`:

```javascript
// In d.start() function:
this.animationLoop = setInterval(this.animate, this.animationTimeout);
```

### Animation Function
The core animation function (`d.animate`) is called every 50ms (20 FPS) and performs:

1. **Debug Mode**: Updates debugger if enabled
2. **Pre-render Check**: Ensures all assets are loaded
3. **Save Context**: Preserves canvas state
4. **UI Rendering**: Draws sidebar and UI elements
5. **Viewport Setup**: Configures clipping region
6. **Map Rendering**: Draws the game map
7. **Grid Rendering**: Draws grid (debug mode only)
8. **Object Updates**: Processes unit/turret orders and movement
9. **Object Rendering**: Draws all game objects
10. **Bullet Rendering**: Draws projectiles
11. **Fog of War**: Renders visibility masking
12. **Restore Context**: Restores canvas state
13. **Message Display**: Shows game messages
14. **Cursor Rendering**: Draws mouse cursor

### Supporting Loops
Additional intervals handle specific game systems:
- **Tiberium Growth**: `setInterval` for tiberium stage progression
- **Mission Status**: `setInterval` for win/loss condition checking

## Rendering System

### Canvas Context
The game uses a single HTML5 Canvas element for all rendering operations:

```javascript
var a = $("#canvas")[0];
var b = a.getContext("2d");
```

### Coordinate System
- Grid-based system with 24x24 pixel tiles (`d.gridSize`)
- World coordinates vs. screen coordinates with viewport translation
- Viewport management for scrolling (`d.viewportX`, `d.viewportY`)

### Sprite Rendering
- Sprite sheets for efficient asset loading
- Directional sprites for units (32 directions)
- Animation frames for buildings/units in different states

### Visual Effects
- Selection boxes for units/buildings
- Health bars
- Fog of war implementation
- Particle effects (bullets)

## Game Entities

### Units (Vehicles/Infantry)
Each unit has properties:
- Position (`x`, `y`)
- Health and hit points
- Movement direction and speed
- Orders and instructions
- Team affiliation
- Collision detection

### Buildings
Building properties include:
- Grid-based placement
- Construction states
- Power input/output
- Health and damage states
- Special functions (refineries, construction yards)

### Turrets
- Automated targeting
- Limited turning speed
- Weapon systems
- Power requirements

## Input Handling

### Mouse System (c)
- Position tracking (`x`, `y`)
- Grid coordinate conversion
- Drag selection
- Object hovering detection
- Cursor state management

### Event Listeners
- **Mouse Movement**: Updates cursor position and object hovering
- **Mouse Click**: Handles unit selection and orders
- **Mouse Down/Up**: Manages drag selection
- **Context Menu**: Right-click orders
- **Keyboard**: Control groups (0-9 with Ctrl)

### Selection System
- Single unit selection
- Multiple unit selection (Shift+Click)
- Drag selection box
- Control group management

## Pathfinding

The game implements A* pathfinding for unit movement:

### Algorithm
- Euclidean distance heuristic
- Grid-based navigation
- Obstruction grid for collision detection
- Dynamic path recalculation

### Collision Detection
- Hard collisions (impassable)
- Soft collisions (unit avoidance)
- Building obstruction mapping
- Terrain type handling

## Sound System (q)

### Audio Implementation
- Web Audio API for sound playback
- Multiple sound banks (voice, sounds, talk)
- Randomized sound selection for variety
- Event-triggered playback

### Sound Categories
- Voice prompts (building, unit selection)
- Sound effects (construction, firing)
- Ambient sounds
- UI feedback

## UI Components

### Sidebar (e)
- Construction options
- Building/unit deployment
- Repair/sell modes
- Power indicator
- Resource display (cash)

### Cursor System
- Context-sensitive cursors
- Animated cursor sprites
- Directional cursors (panning)
- Action indicators

### Fog of War (A)
- Canvas-based visibility system
- Unit sight radius
- Building visibility
- Dynamic fog clearing

## Performance Considerations

### Optimization Techniques
- Sprite sheets for reduced HTTP requests
- Object pooling for bullets
- Efficient rendering (only visible objects)
- Grid-based collision detection
- Lazy loading of assets

### Frame Rate Management
- Fixed 20 FPS update rate
- Animation interpolation
- Selective rendering in debug mode

## Development Notes

### Browser Compatibility
- Works best on Chrome and Firefox
- Requires HTML5 Canvas support
- Web Audio API for sound

### Known Limitations
- Single player only (no multiplayer)
- Limited unit/building variety
- Simplified AI behavior
- No save/load functionality

## Future Enhancements

### Planned Features
- Multiplayer support (Node.js/nowjs)
- More units and buildings
- Expanded campaign
- Improved AI pathfinding
- Enhanced graphics and effects

---

*This documentation is based on analysis of the cnc-v0.3d.js implementation*