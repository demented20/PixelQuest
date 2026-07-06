
# 🎮 PixelQuest : Robot Wars

> **🏆 Best Upcoming Edition Award**  Hackathon Weekly  
> Built in 3 hours with early access to **Fable 5**

---

## What is this?

**Hooded Quest: Robot Wars** is a retro-style side-scrolling platformer that runs entirely in the browser no dependencies, no build step, no install. Just a single HTML file you can open and play. You're a hooded hero fighting your way through 5 machine-overrun worlds, platforming across gaps, stomping and smashing robots, collecting coins, and gearing up at mid-level shops to take on increasingly brutal enemies.

The whole game physics, sprites, level design, shop system, enemy AI, parallax scrolling , lives inside one self-contained `pixel_quest.html`, weighing under 750 lines of vanilla JavaScript.

---

## The Competition

**Hackathon Weekly** is a recurring competitive hackathon format where developers and makers compete in tight time constraints around a central theme. This edition's theme was **Fable 5**, with participants given early model access to build something creative using it. The constraint: **3 hours to build and present**.

The project took home the **Best Upcoming Edition Award** — recognising the most promising build in the category.

---

## Gameplay

You play as a cloaked warrior navigating 5 distinct worlds, each with its own colour theme, terrain layout, and escalating enemy roster. The goal each level is simple: reach the flag at the far end. Getting there is not.

**Worlds:**

| Level | Name | Vibe |
|---|---|---|
| 1 | Emerald Meadow | Bright greens, gentle intro |
| 2 | Deep Forest | Dark teal, tighter platforms |
| 3 | Sunscorch Dunes | Sandy orange, brutal heat |
| 4 | Volcanic Core | Deep crimson, punishing enemies |
| 5 | Neon Midnight | Dark purple, the hardest gauntlet |

**Controls:**

| Key | Action |
|---|---|
| `← →` / `A D` | Move |
| `Space` / `W` / `↑` | Jump |
| `J` | Mace attack |
| `K` | Arc Blaster (ranged) |
| `E` | Enter / leave shop |

---

## Enemies

Five robot types populate the worlds, growing harder as you progress:

| Enemy | HP | Armored | Behaviour |
|---|---|---|---|
| **Scout** | 1 | No | Basic patrol, easy target |
| **Roller** | 1 | No | Faster than Scout, more aggressive |
| **Trooper** | 2 | No | Tougher, takes two hits |
| **Tank** | 3 | ✅ | Immune to stomp needs a mace |
| **Sentinel** | 5 | ✅ | Armored, jumps, 14 coin reward |

Armoured enemies cannot be killed by stomping alone they require melee weapons or a blaster, which you buy at the in-level shop.

---

## Shop System

Each level contains a **Robot-Parts Shop** placed early in the map. Walk into the doorway and press `E` to browse upgrades:

| Item | Cost | Effect |
|---|---|---|
| Iron Mace | 12c | Unlocks melee attack (required for armoured bots) |
| Power Core | 26c | +1 mace damage (requires mace) |
| Arc Blaster | 32c | Ranged projectile, bypasses all armour |
| Repair Kit | 15c | +1 heart (up to max 5) |
| Swift Boots | 20c | Higher movement speed and slightly bigger jumps |

Coins are earned by defeating enemies and collecting pickups scattered across platforms and over pits.

---

## Technical Overview

The entire game is implemented in **a single HTML file** with no external libraries, no frameworks, and no build tooling. Everything is written from scratch.

### Rendering CSS Box Shadow Sprites

There is no `<canvas>`, no WebGL, and no image files. Every visual element — the hero, enemies, coins, projectiles — is rendered using **CSS `box-shadow` pixel art**.

Each sprite is defined as a 2D character grid:

```js
const HERO = [
  "....dddd....",
  "..ddRRRRdd..",
  ".dRRRRRRRRd.",
  // ...
];
```

A `buildShadow()` function maps every non-transparent character to a coloured `box-shadow` entry, offset by `x * scale` and `y * scale`. The result is a single `<div>` of 1×1px that renders an entire sprite through its shadow list — no images, no canvas draw calls.

Sprite mirroring (for left-facing characters) is handled by reversing each row of the character grid before building the shadow string.

### Physics Engine

A custom, minimal physics loop runs at `requestAnimationFrame` speed and handles:

- Velocity and acceleration with friction (`vx *= 0.78` per frame)
- Gravity accumulation capped at terminal velocity
- AABB (axis-aligned bounding box) collision detection against all platforms
- Stomp detection: if the player's downward velocity places them within 28px of an enemy's top, it registers as a stomp
- Pit-fall recovery: the last known safe ground position is stored each frame; falling out of bounds resets the player there

```js
const GRAVITY = 0.8, JUMP_V = -15.8, MOVE_ACC = 0.95, FRICTION = 0.78;
```

### Level Architecture

Levels are defined as pure data objects — no procedural generation, no external files. Each level specifies:

- **`segs`** — ground platform segments as `[x_start, x_end]` pairs (gaps between them are pits)
- **`bricks`** — elevated platforms as `[x, height_above_ground, width]`
- **`enemies`** — spawn list as `[type, startX, patrolMin, patrolMax]`
- **`store`** — x-position of the shop
- **`len`** — total world width (ranges from 4200px to 6000px)

Coins are auto-generated: arcs fly over every pit gap (5 coins per pit, parabolic placement using `Math.sin`), and rows sit atop brick platforms and along ground segments.

### Parallax Scrolling

The background layer (`#bg`) scrolls at `0.35×` the world speed, creating a depth illusion with CSS `clip-path` mountain ridges. World translation and background translation are applied each frame via `transform: translateX()` — no repaints, just composited GPU layer moves.

The camera tracks the player with a 32% viewport offset so the hero stays in the left-centre rather than dead centre.

### Enemy AI

Enemies patrol between a `minX` / `maxX` range and reverse direction on bounds hit. The Sentinel type also jumps at randomised intervals (`jt` countdown timer), making it unpredictable on the final level. Enemy damage triggers a `brightness(2.2)` CSS filter flash for 80ms as visual feedback.

### Combat

- **Melee:** activates a hitbox in front of the player for 12 frames after pressing `J`. Uses a `Set` (`swingHit`) to prevent the same swing from hitting an enemy twice.
- **Ranged:** spawns a bolt object with a fixed lifespan (120 frames) travelling at 9px/frame in the player's facing direction. Bolts are removed on enemy contact or expiry.
- **Invulnerability frames:** after taking damage, the player has 90 frames of invincibility with an opacity flicker effect.

### Shop & State

Game state is held in a single mutable `save` object. The shop reads and modifies this directly, with reactive re-rendering of the shop UI on every purchase. Items that have prerequisites (Power Core requires Mace, Repair Kit requires missing hearts) disable their buy buttons automatically.

---

## Running It

No server needed. Download `pixel_quest.html` and open it in any modern browser.

```
open pixel_quest.html
```

That's it.

---

## Built With

- Vanilla JavaScript (ES6+)
- CSS animations and `box-shadow` sprite rendering
- Zero dependencies
- Built in 3 hours at Hackathon Weekly
