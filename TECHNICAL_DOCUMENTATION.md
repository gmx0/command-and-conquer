# Command & Conquer - HTML5 Technical Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Game Loop Implementation](#game-loop-implementation)
3. [Core Systems Architecture](#core-systems-architecture)
4. [Entity Component System](#entity-component-system)
5. [Rendering Pipeline](#rendering-pipeline)
6. [Input Processing](#input-processing)
7. [Pathfinding and AI](#pathfinding-and-ai)
8. [Collision Detection](#collision-detection)
9. [Resource Management](#resource-management)
10. [Performance Analysis](#performance-analysis)

## Introduction

This document provides a technical deep dive into the implementation details of the Command & Conquer HTML5 recreation. The focus is on the core systems that make the RTS gameplay possible, including the game loop, entity management, rendering, and AI systems.

## Game Loop Implementation

### Main Animation Loop

The heart of the game is the animation loop implemented in the `d.start()` function:

```javascript
this.animationLoop = setInterval(this.animate, this.animationTimeout);
```

Where:
- `this.animationTimeout = 50` (50ms interval = 20 FPS)
- `this.animate` is the core update function

### The Animate Function

The `d.animate` function is the central update method that orchestrates all game systems:

```javascript
animate: function() {
    // Debug mode update
    d.debugMode && d.showDebugger();
    
    // Pre-render check - ensure all assets are loaded
    if (!p.loaded || !e.loaded || !l.loaded || !j.loaded || !g.loaded) {
        b.clearRect(0, 0, a.width, a.height);
        return;
    }
    
    // Rendering and update sequence
    b.save();                    // Save canvas state
    e.draw();                    // Draw UI/sidebar
    d.setViewport();             // Set clipping region
    d.drawMap();                 // Render map
    d.debugMode && d.drawGrid(); // Debug grid (if enabled)
    d.moveObjects();             // Update unit/turret positions
    d.drawObjects();             // Render all game objects
    d.drawBullets();             // Render projectiles
    d.debugMode || A.draw();     // Fog of war
    b.restore();                 // Restore canvas state
    d.drawMessage();             // Game messages
    c.draw();                    // Mouse cursor
}
```

### Supporting Systems Loops

Additional timed functions handle specific game systems:

1. **Tiberium Growth**:
   ```javascript
   this.tiberiumLoop = setInterval(function() {
       for (var a = 0; a < d.overlay.length; a++) {
           var b = d.overlay[a];
           b.name == "tiberium" & b.stage < 11 && b.stage++;
       }
   }, d.animationTimeout * 40 * 600);
   ```

2. **Mission Status**:
   ```javascript
   this.statusLoop = setInterval(d.missionStatus, 3e3);
   ```

### Frame Timing and Synchronization

The game uses a fixed timestep approach:
- **Update Rate**: 20 FPS (50ms per frame)
- **Interpolation**: Movement is interpolated based on speed factors
- **Synchronization**: All systems update at the same rate

## Core Systems Architecture

### Global Namespace Objects

The game uses a collection of global objects to manage different systems:

| Object | Purpose |
|--------|---------|
| `c` | Mouse/cursor system |
| `d` | Main game state and core functions |
| `e` | Sidebar UI system |
| `g` | Buildings system |
| `j` | Infantry system |
| `l` | Vehicles system |
| `m` | Turrets system |
| `o` | Overlay system |
| `p` | Level/loading system |
| `q` | Sound system |
| `A` | Fog of war system |

### Game State Management

The main game object `d` maintains state through arrays:

```javascript
d = {
    units: [],          // Active units
    buildings: [],      // Active buildings
    turrets: [],        // Active turrets
    overlay: [],        // Map overlays (tiberium, trees)
    bullets: [],        // Active projectiles
    selectedItems: [],  // Currently selected entities
    selectedAttackers: [], // Selected units with weapons
    selectedUnits: []   // Selected mobile units
}
```

### Coordinate Systems

The game uses multiple coordinate systems:
- **World Coordinates**: Absolute positions in the game world
- **Grid Coordinates**: Tile-based positions (24x24 pixel tiles)
- **Screen Coordinates**: Viewport-relative positions for rendering
- **Viewport Management**: Scrolling through the game world

```javascript
// Coordinate conversion in mouse system (c)
c.gameX = c.x + d.viewportX - d.viewportLeft;
c.gameY = c.y + d.viewportY - d.viewportTop;
c.gridX = Math.floor(c.gameX / d.gridSize);
c.gridY = Math.floor(c.gameY / d.gridSize);
```

## Entity Component System

### Base Entity Structure

All game entities share a common structure with type-specific properties:

```javascript
// Generic entity properties
{
    x: 0,               // World X position
    y: 0,               // World Y position
    team: "gdi",        // Team affiliation
    health: 100,        // Current health
    hitPoints: 100,     // Maximum health
    sight: 3,           // View distance in tiles
    status: "healthy",  // Damage state
    selected: false     // Selection state
}
```

### Unit Entities (Vehicles/Infantry)

Units have additional properties for movement and AI:

```javascript
{
    // Movement properties
    moveDirection: 0,       // Direction (0-31)
    speed: 12,              // Movement speed
    turnSpeed: 5,           // Turning speed
    
    // AI properties
    orders: {               // Current orders
        type: "patrol",
        from: {x: 10, y: 10},
        to: {x: 20, y: 20}
    },
    instructions: [],       // Movement instructions
    
    // Combat properties
    primaryWeapon: 9,       // Weapon type
    reloadTime: 2000,       // Reload time (ms)
    bulletFiring: false     // Firing state
}
```

### Building Entities

Buildings have construction and power-related properties:

```javascript
{
    // Construction properties
    status: "build",        // build/healthy/destroyed
    gridShape: [[1,1,1],[1,1,1]], // Occupied tiles
    
    // Power properties
    powerIn: 20,            // Power consumption
    powerOut: 100,          // Power generation
    
    // Economic properties
    cost: 2000,             // Build cost
    tiberiumStorage: 1000   // Storage capacity (refineries)
}
```

## Rendering Pipeline

### Canvas Context Management

The game uses a single 2D canvas context for all rendering:

```javascript
var a = $("#canvas")[0];  // Canvas element
var b = a.getContext("2d"); // 2D context
```

### Viewport System

The rendering pipeline uses a viewport system for scrolling:

```javascript
setViewport: function() {
    b.beginPath();
    this.viewportWidth = e.visible ? this.screenWidth - e.width : this.screenWidth;
    this.viewportHeight = 480;
    b.rect(this.viewportLeft, this.viewportTop, 
           this.viewportWidth - this.viewportLeft, this.viewportHeight);
    b.clip(); // Clip rendering to viewport
}
```

### Object Rendering Order

Objects are rendered in a specific order to ensure proper layering:

1. Map background
2. Overlay elements (tiberium, trees)
3. Buildings
4. Turrets
5. Units
6. Bullets
7. Selection indicators
8. Fog of war

### Sprite Animation

Entities use sprite sheets with animation frames:

```javascript
// Animation loop in entity draw functions
this.animationIndex++;
if (this.animationIndex / this.animationSpeed >= frameCount) {
    this.animationIndex = 0;
}
var frame = Math.floor(this.animationIndex / this.animationSpeed);
```

## Input Processing

### Mouse System (c)

The mouse system handles all pointer interactions:

```javascript
listenEvents: function() {
    $("#canvas").mousemove(function(a) {
        // Update mouse position
        var b = $("#canvas").offset();
        c.x = a.pageX - b.left;
        c.y = a.pageY - b.top;
        
        // Update grid coordinates
        c.gridX = Math.floor(c.gameX / d.gridSize);
        c.gridY = Math.floor(c.gameY / d.gridSize);
        
        // Handle drag selection
        if (c.buttonPressed) {
            if (Math.abs(c.dragX - c.gameX) > 5 || 
                Math.abs(c.dragY - c.gameY) > 5) {
                c.dragSelect = true;
            }
        } else {
            c.dragSelect = false;
        }
    });
    
    // Click handling
    $("#canvas").click(function(a) {
        return c.click(a, false), c.dragSelect = false, false;
    });
}
```

### Selection System

The selection system manages unit/building selection:

```javascript
selectItem: function(a, b) {
    if (b && a.selected) {
        // Deselect item
        a.selected = false;
        this.selectedItems.remove(a);
        this.selectedUnits.remove(a);
        this.selectedAttackers.remove(a);
    } else {
        // Select item
        a.selected = true;
        this.selectedItems.push(a);
        
        // Add to appropriate selection groups
        if (a.type != "building" && a.team == d.currentLevel.team) {
            this.selectedUnits.push(a);
            q.play(a.type + "_select");
            
            if (a.primaryWeapon) {
                this.selectedAttackers.push(a);
            }
        }
    }
}
```

### Order Issuance

Orders are issued through click interactions:

```javascript
// In d.click() function
if (d.selectedUnits.length > 0) {
    if (!d.obstructionGrid[c.gridY] || 
        d.obstructionGrid[c.gridY][c.gridX] != 1 || 
        !!c.isOverFog) {
        
        // Issue move order to all selected units
        for (var n = d.selectedUnits.length - 1; n >= 0; n--) {
            d.selectedUnits[n].orders = {
                type: "move", 
                to: {x: c.gridX, y: c.gridY}
            };
            q.play(d.selectedUnits[n].type + "_move");
        }
    }
}
```

## Pathfinding and AI

### A* Pathfinding Implementation

The game uses an A* implementation for unit navigation:

```javascript
function z(a, c, e) {
    var f = e ? d.heroObstructionGrid : d.obstructionGrid;
    
    // Set start/end points as traversable
    try {
        f[c[1]][c[0]] = 0;
        f[a[1]][a[0]];
    } catch (g) {
        return [{x: a[0], y: a[1]}, {x: c[0], y: c[1]}];
    }
    
    // Run A* algorithm
    var h = H(f, a, c, "Euclidean");
    G(h, f); // Path smoothing
    
    return h;
}
```

### Path Smoothing

Paths are smoothed to reduce zigzag movement:

```javascript
function G(a, b) {
    var c = true, d = a[0];
    
    while (c && a.length > 2) {
        var e = a[2];
        
        // Check if we can skip intermediate points
        if (Math.abs(e.y - d.y) > Math.abs(e.x - d.x)) {
            // Vertical movement optimization
            // ... implementation details
        } else {
            // Horizontal movement optimization
            // ... implementation details
        }
        
        c && a.splice(1, 1); // Remove unnecessary points
    }
}
```

### AI Behavior Patterns

Units implement several AI behaviors:

1. **Patrol**: Move between two points
2. **Guard**: Protect a target
3. **Attack**: Engage enemies
4. **Harvest**: Collect tiberium
5. **Protect**: Escort units

```javascript
// Example: Patrol behavior
orders: {
    type: "patrol",
    from: {x: 9, y: 24},
    to: {x: 12, y: 8}
}
```

## Collision Detection

### Collision Types

The game implements three types of collisions:

1. **Hard Collision**: Impassable objects
2. **Soft Collision**: Unit avoidance
3. **Soft-Hard Collision**: Buildings and terrain

```javascript
collision: function(a) {
    if (this == a) return false;
    
    var b = Math.pow(this.x - a.x, 2) + Math.pow(this.y - a.y, 2);
    var c = Math.pow((this.collisionRadius + a.collisionRadius) / d.gridSize, 2);
    var e = Math.pow((this.softCollisionRadius + a.collisionRadius) / d.gridSize, 2);
    var f = Math.pow((this.softCollisionRadius + a.softCollisionRadius) / d.gridSize, 2);
    
    if (b > c) {
        if (b < e) {
            return {type: "soft-hard", distance: Math.pow(b, .5)};
        } else if (b > f) {
            return false;
        } else {
            return {type: "soft", distance: Math.pow(b, .5)};
        }
    } else {
        return {type: "hard", distance: Math.pow(b, .5)};
    }
}
```

### Collision Response

Units respond to collisions with steering behaviors:

```javascript
// In moveTo function
if (this.colliding) {
    var m = F(this.collisionWith, this, 32);
    var n = D(this.moveDirection, m, 32);
    var o = D(f, m, 32);
    
    switch (this.collisionType) {
        case "hard":
            this.movementSpeed = 0;
            // Turn away from collision
            // ... implementation
            break;
            
        case "soft-hard":
            // Slow down and turn
            // ... implementation
            break;
            
        case "soft":
            // Adjust speed based on distance
            // ... implementation
            break;
    }
}
```

## Resource Management

### Asset Loading

The game implements a custom asset loading system:

```javascript
preloadImage: function(a, b) {
    var c = this;
    this.loaded = false;
    var d = new Image;
    return d.src = "images/" + a, this.preloadCount++,
    $(d).bind("load", function() {
        c.loadedCount++;
        c.loadedCount == c.preloadCount && (c.loaded = true);
        b && b();
    }), d;
}
```

### Sprite Sheet Management

Sprite sheets are used for efficient asset loading:

```javascript
loadSpriteSheet: function(a, b, c) {
    a.spriteCanvas = document.createElement("canvas");
    a.spriteImage = this.preloadImage(c + "/" + b.name + "-sprite-sheet.png", 
        function() {
            i(a, b);
        });
    a.spriteArray = [];
    a.spriteCount = 0;
    
    // Load sprite definitions
    for (var d = 0; d < b.imagesToLoad.length; d++) {
        var e = b.imagesToLoad[d].count;
        var f = b.imagesToLoad[d].name;
        a.spriteArray[f] = {
            name: f, 
            count: e, 
            offset: a.spriteCount
        };
        a.spriteCount += e;
    }
}
```

### Memory Management

The game uses object pooling for bullets to reduce garbage collection:

```javascript
fireBullet: function(a) {
    a.x = a.x - .5 * Math.sin(a.angle);
    a.y = a.y - .5 * Math.cos(a.angle);
    a.range = a.range - .5;
    this.bullets.push(a);
    
    // Reset firing state after reload time
    setTimeout(function() {
        a.source.bulletFiring = false;
    }, a.source.reloadTime);
}
```

## Performance Analysis

### Bottlenecks

1. **Rendering**: Canvas operations for large numbers of units
2. **Pathfinding**: A* calculations for multiple units
3. **Collision Detection**: Checking against all entities
4. **DOM Manipulation**: jQuery overhead in some operations

### Optimization Strategies

1. **Object Pooling**: Reuse bullet objects
2. **Selective Rendering**: Only render visible objects
3. **Efficient Data Structures**: Arrays for entity storage
4. **Batch Operations**: Group similar operations together

### Frame Rate Considerations

The fixed 20 FPS update rate provides a balance between:
- Smooth animation
- Reasonable CPU usage
- Consistent gameplay

Higher frame rates would provide smoother visuals but increase CPU load, particularly for pathfinding and collision detection systems.

---

*This technical documentation provides insight into the implementation details of the Command & Conquer HTML5 recreation, focusing on the core systems that enable RTS gameplay in a web browser.*