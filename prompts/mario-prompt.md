# Original prompt (as sent)

Act as an expert senior game developer and frontend engineer. Your task is to write a complete, functional, and self-contained HTML5 Super Mario clone using a single-file structure (HTML with embedded CSS and JavaScript) or a highly structured modular setup if necessary.

The game must run smoothly in modern web browsers using standard web APIs. Do not use external engines like Phaser or PixiJS; build it using a vanilla JavaScript 2D Canvas context (`canvas.getContext('2d')`).

Here are the strict specifications for the game:

### 1. Core Architecture & Game Loop
- Use `requestAnimationFrame` for a smooth 60fps game loop.
- Implement a robust Delta Time calculation to ensure movement and physics are frame-rate independent.
- Implement an asset loading system that preloads placeholder graphics (colored rectangles/simple shapes) so the game is immediately playable, with clear code hooks where real image sprites/spritesheets can be swapped in.
- Create a basic State Machine to manage game screens: Start Screen, Playing, Paused, Game Over, and Level Complete.

### 2. Controls & Input Handling
- Implement Keyboard Listeners for standard desktop controls:
  - Left/Right Arrow keys or A/D keys for movement.
  - Up Arrow, W, or Spacebar for jumping.
  - Shift or X for running faster/shooting (if power-up active).
- Ensure input handling is responsive, allowing for simultaneous key presses (e.g., jumping while running).

### 3. Physics & Movement Engine
- Implement a 2D platformer physics engine featuring:
  - Velocity, acceleration, friction, and gravity.
  - Variable jump height (holding the jump key results in a higher jump, tapping it results in a short hop).
  - Momentum: The player should have slight sliding friction when stopping or changing directions quickly.
- Implement strict AABB (Axis-Aligned Bounding Box) collision detection between:
  - The player and solid environment tiles (floors, walls, ceilings).
  - The player and enemies.
  - Enemies and solid environment tiles.

### 4. Player Mechanics (Mario-like)
- **States:** Implement at least Small (can take 1 hit to die) and Super/Big (can break blocks, takes 1 hit to shrink back to Small).
- **Abilities:** Walking, running, jumping, ducking, and falling.
- **Feedback:** Add temporary invulnerability frames (flashing animation) after getting hit.

### 5. Level Design & Camera System
- Generate a grid-based level array (at least 200 tiles wide and 15 tiles high).
- The level should include:
  - Solid ground and floating platforms.
  - Breakable brick blocks (shatter animation/particles when hit from below by Super Mario).
  - "?" Mystery Blocks that release a coin or a power-up when hit from below, then transform into an inert solid block.
  - Pipes that act as solid obstacles.
- Implement a side-scrolling camera system that smoothly tracks the player horizontally but stops at the left and right level boundaries. Prevent the player from moving off-screen to the left.

### 6. Entities & AI (Enemies & Items)
- **Goomba Enemy:** Walks back and forth. Changes direction upon hitting a wall/pipe. Squished and destroyed if the player jumps on its head. Kills/shrinks the player if touched from the sides or bottom.
- **Mushroom/Flower Item:** Spawns from "?" blocks. Moves horizontally, falls under gravity, and changes direction on wall collisions. Collecting it upgrades the player.
- **Coins:** Floating in the air or spawned from blocks. Instantly collected by the player to increase score.

### 7. User Interface (UI) & HUD
- Overlay a clean HUD at the top of the canvas during gameplay showing:
  - Score (MARIO)
  - Coin Counter (x00)
  - World Name (1-1)
  - Remaining Time (countdown timer)

### 8. Sound & Visual Effects Hooks
- Include dummy functions/placeholders for sound effects (Jump, Coin, Power-up, Die, Brick Break).
- Implement a simple particle system for when blocks break or enemies get squished.

Deliver the complete, fully commented HTML/JS code block. Ensure the physics code is robust enough to prevent the player from falling through the floor or clipping into walls.
