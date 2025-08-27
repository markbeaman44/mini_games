# Mini Games Hub - AI Coding Instructions

## Project Overview
This is a self-contained HTML5 mini-games collection targeting kids and families. Each game is a single HTML file with embedded CSS/JavaScript using minimal dependencies. The hub (`home.html`) serves as the main navigation.

## Architecture Patterns

### Single-File Game Structure
- **Pattern**: Each game is completely self-contained in one `.html` file
- **Why**: Simplifies deployment, sharing, and maintenance for non-technical users
- **Components**: Embedded `<style>` block, game logic in `<script>` block, minimal HTML structure
- **Example**: `luckydice.html` contains Pokemon dice game with 700+ lines including styles, game logic, and UI

### Navigation System
- **Back Button**: Fixed position (`position: fixed; top: 20px; left: 20px`) with consistent styling
- **Hub Integration**: All games link back to `home.html` via back button click handler
- **Pattern**: `window.location.href = 'home.html'` in back button event listeners

### Game State Management
- **Local Storage**: Games persist high scores using `localStorage.getItem()`/`setItem()`
- **Pattern**: `bestScore = parseInt(localStorage.getItem('gameNameBest') || '0')`
- **Game Loop**: Most action games use `requestAnimationFrame()` with update/draw pattern
- **State Objects**: Player state stored in objects like `{ x, y, w, h, speed, ... }`

### Game Container & Layout Pattern (CRITICAL FOR CENTERING)
- **Game Wrapper**: ALWAYS use `.game-wrapper` with flexbox centering for consistent tablet/mobile layout
- **Responsive Sizing**: `width: min(900px, 95vw); height: min(600px, 85vh)` for game containers
- **NEVER use body flexbox**: Do NOT put `display: flex` directly on the body element - this causes centering issues on tablets
- **Required Centering Pattern**: 
  ```css
  body {
    height: 100vh;
    height: 100dvh;
    margin: 0;
    overflow: hidden;
    /* NO display: flex here! */
  }
  
  .game-wrapper {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
    box-sizing: border-box;
  }
  ```
- **Mandatory HTML Structure**: `<body><div class="game-wrapper"><div class="game-container">...</div></div></body>`
- **Common Mistake**: Putting flexbox centering directly on body causes games to appear too high or too low on tablets
- **Working Examples**: Reference `panda.html`, `hideseek.html`, `spaceexplorer.html` for correct implementation

### Tablet Controls Pattern (REQUIRED FOR ALL GAMES)

**Tablet Controls (Required for all games):**
- On screens between 720px and 1285px wide, display tablet controls outside the game container.
- Controls: left/right buttons on the left side, up/down buttons on the right side (outside the game container, vertically centered).
- Controls must use touch events and update the game logic (e.g., set keys.ArrowLeft, keys.ArrowRight, keys.ArrowUp, keys.ArrowDown).
- Controls must be hidden for screens below 720px and above 1285px.
- Example HTML:
  ```html
  <div id="tabletControlsLeft" class="tablet-controls tablet-controls-left">
    <button id="tabletLeft" class="tablet-btn">◀</button>
    <button id="tabletRight" class="tablet-btn">▶</button>
  </div>
  <div id="tabletControlsRight" class="tablet-controls tablet-controls-right">
    <button id="tabletUp" class="tablet-btn">⬆</button>
    <button id="tabletDown" class="tablet-btn">⬇</button>
  </div>
  ```
- Example CSS:
  ```css
  .tablet-controls {
    position: fixed;
    top: 0;
    bottom: 0;
    display: none;
    flex-direction: column;
    justify-content: center;
    gap: 18px;
    z-index: 999;
    pointer-events: none;
  }
  .tablet-controls-left {
    left: 0;
    width: 80px;
    align-items: flex-end;
    padding-left: 10px;
  }
  .tablet-controls-right {
    right: 0;
    width: 80px;
    align-items: flex-start;
    padding-right: 10px;
  }
  .tablet-btn {
    width: 56px;
    height: 56px;
    border-radius: 12px;
    border: none;
    font-weight: 800;
    background: rgba(255,255,255,0.95);
    box-shadow: 0 6px 18px rgba(0,0,0,0.12);
    font-size: 28px;
    cursor: pointer;
    user-select: none;
    -webkit-tap-highlight-color: transparent;
    pointer-events: auto;
    margin: 0 0 0 0;
  }
  @media (min-width: 720px) and (max-width: 1285px) {
    .tablet-controls {
      display: flex;
    }
  }
  @media (max-width: 719px), (min-width: 1286px) {
    .tablet-controls {
      display: none !important;
    }
  }
  ```
- Example JS:
  ```javascript
  // Tablet controls event listeners
  const tabletLeft = document.getElementById('tabletLeft');
  const tabletRight = document.getElementById('tabletRight');
  const tabletUp = document.getElementById('tabletUp');
  const tabletDown = document.getElementById('tabletDown');

  tabletLeft.addEventListener('touchstart', e => { keys.ArrowLeft = true; e.preventDefault(); });
  tabletLeft.addEventListener('touchend', e => { keys.ArrowLeft = false; e.preventDefault(); });
  tabletRight.addEventListener('touchstart', e => { keys.ArrowRight = true; e.preventDefault(); });
  tabletRight.addEventListener('touchend', e => { keys.ArrowRight = false; e.preventDefault(); });
  tabletUp.addEventListener('touchstart', e => {
    keys.ArrowUp = true;
    setTimeout(() => keys.ArrowUp = false, 150);
    e.preventDefault();
  });
  tabletDown.addEventListener('touchstart', e => { keys.ArrowDown = true; e.preventDefault(); });
  tabletDown.addEventListener('touchend', e => { keys.ArrowDown = false; e.preventDefault(); });
  ```

**This pattern is required for all future games and levels.**
For games with grids, boards, or multiple game elements (like dice games, puzzle games):
- **Constrain Board Height**: Use `max-height: 40vh` (or 30vh on tablets/mobile) to prevent boards from taking entire screen
- **Set Maximum Element Sizes**: Use `max-width` and `max-height` on grid cells/board spaces (e.g., `max-width: 70px`)
- **Responsive Grid Cells**: Use `aspect-ratio: 1` with CSS Grid `1fr` columns, but constrain with max dimensions
- **Reserve Space**: Ensure UI elements (dice, buttons, controls) have enough space below/around the board
- **Example Pattern**:
  ```css
  .board-path {
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    max-height: 40vh; /* Prevent board from dominating screen */
  }
  .board-space {
    aspect-ratio: 1;
    max-width: 70px; /* Constrain individual cells */
    max-height: 70px;
  }
  ```

## Critical Patterns

### Mobile/Touch Support
```javascript
// Standard mobile controls pattern
if ('ontouchstart' in window) {
  mobileControls.style.display = 'block';
}
// Touch button event handling
touchBtn.addEventListener('touchstart', (e) => { e.preventDefault(); });
```

### Mobile Optimization (REQUIRED)
**Critical mobile improvements that MUST be applied to every game file:**

#### CSS Body Height & Overflow
All games must include these CSS properties for proper mobile display:
```css
html,body {
  height: 100vh;
  height: 100dvh;  /* Dynamic viewport height for mobile */
  margin: 0;
  overflow: hidden;
  /* ... other existing styles ... */
}
```
- **`100vh/100dvh`**: Ensures full viewport coverage; `100dvh` accounts for mobile UI elements like address bars
- **`margin: 0`**: Removes default margins that can cause scrolling
- **`overflow: hidden`**: Prevents unwanted scrolling and creates immersive full-screen experience

#### Mobile Address Bar Hide Fix
Add this JavaScript to the end of every game file (before closing `</script>` tag):
```javascript
window.addEventListener("load", function() {
    setTimeout(() => {
        window.scrollTo(0, 1);
    }, 100);
});
```
- **Purpose**: Forces mobile browsers to hide the address bar after page load
- **Timing**: 100ms delay ensures page is fully loaded before scroll attempt
- **Effect**: Maximizes game screen real estate on mobile devices

### Game Screen States
- **Instructions Popup**: Required modal that appears on game load with gameplay instructions
- **Game Area**: Visible during gameplay  
- **Game Over**: Overlay that appears with final stats and restart options
- **Pattern**: Instructions popup → Space/Continue button → Game starts immediately (no separate start screen)
- **Spacebar Control**: Space bar must work for ALL screen states:
  - Instructions Popup: Space bar closes instructions and starts game (same as continue button)  
  - During Gameplay: Space bar performs game action or restarts level
  - Game Over: Space bar restarts the level/game

### Keyboard Event Pattern
```javascript
// Required spacebar handling for all screen states
document.addEventListener('keydown', (e) => {
    // Handle spacebar for instructions popup
    if (e.key === ' ' && this.instructionsPopup.style.display === 'flex') {
        e.preventDefault();
        this.hideInstructions(); // This should start the game
        return;
    }

    // Game controls during gameplay
    if (this.gameArea.style.display === 'block') {
        // ... game-specific controls
    }

    // Handle spacebar for game over
    if (e.key === ' ' && this.gameOver.style.display === 'flex') {
        e.preventDefault();
        this.restartGame();
    }
});
```

### Audio Implementation
```javascript
// Consistent audio pattern across games
function playSound(frequency, duration, type = 'sine') {
  const audioContext = new (window.AudioContext || window.webkitAudioContext)();
  // ... oscillator setup
}
```

### Camera Following
- **When Needed**: Platformers, running games (like Speed Runner), or any game where character moves beyond viewport
- **Smooth Tracking**: Camera smoothly follows the character during movement/flight
- **Centered View**: Keeps character roughly centered in the viewport
- **Smooth Interpolation**: Camera movement uses easing for natural feel
- **Implementation**: Use CSS transforms on a `.game-viewport` container with CSS transitions
- **Pattern**: `gameViewport.style.transform = 'translateX(-${cameraX}px)'` with smooth interpolation
- **Not Needed**: Static puzzle games, card games, or games with fixed viewports

### Canvas Games vs DOM Games
- **Canvas**: Action games like `spaceexplorer.html`, `catcher.html`
- **DOM**: Puzzle games like `puzzle.html`, `pathfinder.html`
- **Mixed**: Some games use canvas for effects with DOM UI

### Asset Management Strategy
- **Preferred**: Create custom SVG/CSS graphics embedded in the HTML file
- **Fallback**: Use emoji when custom graphics aren't feasible
- **Why**: Custom graphics provide better visual consistency, scalability, and control
- **Implementation**: Inline SVG in HTML or CSS-drawn shapes using borders/gradients
- **Example**: Player sprites as CSS divs with `border-radius` and `background` styling
- **Avoid**: External image files that break the single-file architecture

## Development Workflow

### Adding New Games
1. Copy existing similar game as template
2. Update `home.html` games grid with new entry
3. Change status from "coming-soon" to "available" class
4. **Apply mobile optimizations**: Add mobile CSS and scroll fix
5. Test back button navigation and mobile responsiveness

### Game Testing Checklist
- Back button navigation to hub
- Local storage high score persistence
- Mobile touch controls (if applicable)
- Keyboard controls (spacebar/enter to start/restart)
- Space bar restarts the level/game
- Game over state and restart functionality
- A popup with instructions needs to be included
- **Mobile viewport**: Test `100vh/100dvh` height and `overflow: hidden`
- **Mobile address bar**: Verify address bar hides on mobile devices
- **CRITICAL - Tablet Centering**: Verify game is properly centered on tablet screens using game-wrapper pattern

### Debugging Common Issues
- **Pokemon encounters not working**: Check `getSpaceType()` function exists and HTML IDs match JavaScript selectors
- **Mobile controls**: Verify touch event handlers and `touchstart`/`touchend` events
- **High DPI displays**: Most canvas games include `fixHiDPI()` function for device pixel ratio
- **Tablet centering issues**: If games appear too high/low on tablets, ensure using game-wrapper pattern instead of body flexbox

## Code Conventions

### Game Structure Template
```html
<!DOCTYPE html>
<html>
<head>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    
    body {
      font-family: 'Arial', sans-serif;
      background: linear-gradient(/* game-specific gradient */);
      height: 100vh;
      height: 100dvh;
      margin: 0;
      overflow: hidden;
      color: white;
    }
    
    .game-wrapper {
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 20px;
      box-sizing: border-box;
    }
    
    .game-container {
      width: min(900px, 95vw);
      height: min(600px, 85vh);
      background: /* game-specific background */;
      border-radius: 20px;
      position: relative;
      overflow: hidden;
    }
    /* Additional game-specific styles */
  </style>
</head>
<body>
  <button id="backButton">← Back</button>
  <div class="game-wrapper">
    <div class="game-container">
      <!-- Instructions Popup (shown first) -->
      <div class="instructions-popup" id="instructionsPopup">
        <div class="instructions-content">
          <h2>How to Play</h2>
          <p>Game instructions...</p>
          <button class="close-instructions" id="closeInstructions">Start Game!</button>
        </div>
      </div>
      <!-- Game Area (shown after instructions) -->
      <div class="game-area" id="gameArea"><!-- Game content --></div>
      <!-- Game Over (overlay) -->
      <div class="game-over" id="gameOver"><!-- Game over content --></div>
    </div>
  </div>
  <script>
    /* Game logic */
    
    // Mobile address bar hide fix - REQUIRED
    window.addEventListener("load", function() {
      setTimeout(() => {
        window.scrollTo(0, 1);
      }, 100);
    });
  </script>
</body>
</html>
```

### Naming Conventions
- Game files: `lowercase.html` (e.g., `luckydice.html`)
- CSS classes: `kebab-case` (e.g., `game-container`, `start-screen`)
- JavaScript: `camelCase` for variables/functions
- IDs: `camelCase` (e.g., `startBtn`, `gameArea`)

### Performance Considerations
- Games target 60fps for smooth experience
- Use `requestAnimationFrame()` for animations
- Minimize DOM manipulation during game loops
- Canvas rendering preferred for intensive graphics

## Testing Approach
- Manual testing on desktop and mobile browsers
- Focus on core game mechanics and navigation
- No automated testing framework - games are simple enough for manual QA
- Test localStorage persistence across browser sessions

## Deployment
- Static files only - no build process required
- Can be served from any web server or opened directly in browser
- All assets embedded in HTML files for portability
