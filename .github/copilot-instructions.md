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

### Visual Design System
- **Gradients**: Heavy use of `linear-gradient()` and `radial-gradient()` for backgrounds
- **Game Container**: Standard pattern `width: min(900px, 95vw); height: min(600px, 85vh)`
- **Centering**: Use flexbox centering on body: `display: flex; flex-direction: column; align-items: center; justify-content: center;`
- **No Margin**: Game container should NOT use `margin: auto` - let flexbox handle centering
- **Responsive**: `@media` queries for mobile with touch controls
- **Color Themes**: Each game has distinct color palette (space = neon, puzzle = pastels, etc.)

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

### Debugging Common Issues
- **Pokemon encounters not working**: Check `getSpaceType()` function exists and HTML IDs match JavaScript selectors
- **Mobile controls**: Verify touch event handlers and `touchstart`/`touchend` events
- **High DPI displays**: Most canvas games include `fixHiDPI()` function for device pixel ratio

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
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      color: white;
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
  <button id="backButton">← Back to Hub</button>
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
