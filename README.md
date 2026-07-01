[skill_3lperspecitveparallax.md](https://github.com/user-attachments/files/29546817/skill_3lperspecitveparallax.md)
```markdown
---
name: skill-three-layer-parallax
description: Drag-controlled three-layer perspective parallax with Z-axis depth. Use for immersive product showcases, interactive backgrounds, or storytelling pages where foreground, window, and background move at different speeds and depths.
---

# Three-Layer Perspective Parallax

Use this skill for single-file HTML pages that create a 3D parallax effect controlled by mouse or touch drag. Three layers rotate independently with different max angles and static Z-axis offsets, producing a "mirror tilt" feel.

## Recommended flow

1. Create a scene container with `perspective: 1200px`.
2. Position three layers: foreground (text), window-frame (middleground), background (image inside window).
3. Assign each layer its own `MAX_ANGLE` and `translateZ` offset.
4. Map drag distance to rotation angles with clamped normalization.
5. Apply transforms with `rotateX` and `rotateY`.
6. Support double-click reset and touch events.

## When to use

- Product showcases
- Immersive background interaction
- Storytelling pages
- UI elements that need 3D depth
- Single-file HTML deliverables

## When not to use

- When you need true 3D models
- For complex 3D scenes requiring WebGL
- When users expect tilt based on device orientation
- For small UI components where parallax is overkill

## Core pattern

```javascript
const MAX_FG = 27;
const MAX_WIN = 25;
const MAX_BG = 29;

function updateAngles(deltaX, deltaY) {
    const rect = scene.getBoundingClientRect();
    const maxDeltaX = rect.width * 0.5;
    const maxDeltaY = rect.height * 0.5;
    let normX = deltaX / maxDeltaX;
    let normY = deltaY / maxDeltaY;
    normX = Math.min(1, Math.max(-1, normX));
    normY = Math.min(1, Math.max(-1, normY));

    const fgAngleY = normX * MAX_FG;
    const fgAngleX = -normY * MAX_FG;
    const winAngleY = normX * MAX_WIN;
    const winAngleX = -normY * MAX_WIN;
    const bgAngleY = normX * MAX_BG;
    const bgAngleX = -normY * MAX_BG;

    foreground.style.transform = `translate(-50%, -50%) translateZ(180px) rotateX(${fgAngleX}deg) rotateY(${fgAngleY}deg)`;
    windowFrame.style.transform = `translate(-50%, -50%) translateZ(0px) rotateX(${winAngleX}deg) rotateY(${winAngleY}deg)`;
    backgroundLayer.style.transform = `translateZ(-300px) rotateX(${bgAngleX}deg) rotateY(${bgAngleY}deg)`;
}
```

## Layer structure

The three layers are nested independently inside the scene container:

```html
<div class="scene" id="scene">
    <!-- Foreground: text -->
    <div class="foreground-text" id="foregroundText">
        🌌 Title
        <small>Subtitle</small>
    </div>

    <!-- Middleground: window frame -->
    <div class="window-frame" id="windowFrame">
        <!-- Background: image inside window -->
        <div class="background-layer" id="backgroundLayer">
            <img src="image.jpg" alt="Background">
        </div>
        <!-- window decorations -->
    </div>
</div>
```

## Fixed Z-axis depth (static, not animated)

Z-axis offsets are static positions, not part of the animation. They establish spatial depth between layers.

| Layer | Z offset | Effect |
|-------|----------|--------|
| Foreground | `+180px` | Closest to viewer |
| Window | `0px` | Neutral reference |
| Background | `-300px` | Farthest from viewer |

## Recommended parameters

| Parameter | Suggested range | Description |
|-----------|----------------|-------------|
| `MAX_FG` | 20° – 45° | Foreground rotation sensitivity |
| `MAX_WIN` | 20° – 45° | Window rotation sensitivity |
| `MAX_BG` | 20° – 45° | Background rotation sensitivity |
| `translateZ` (FG) | +80px to +200px | Foreground depth |
| `translateZ` (BG) | -120px to -400px | Background depth |
| `perspective` | 800px – 1500px | Perspective intensity |

## Final validated values (2026-07-01)

| Layer | Max Angle | Z offset |
|-------|-----------|----------|
| Foreground | `27°` | `+180px` |
| Window | `25°` | `0px` |
| Background | `29°` | `-300px` |

## Interaction behavior

- **Drag (mouse/touch)** : All layers rotate proportionally based on their `MAX_ANGLE`.
- **Double-click** : Smoothly resets all angles to `0°`.
- **Smooth transition** : Linear transition during drag; spring-like easing on reset.
- **Performance** : Uses `will-change: transform` and requestAnimationFrame throttling (16ms frame interval).

## Troubleshooting

- If the scene feels too sensitive, reduce all `MAX_ANGLE` values proportionally.
- If depth feels weak, increase `perspective` or widen the Z gap between foreground and background.
- If text becomes unreadable, reduce foreground rotation or increase its Z offset.
- If the image is cropped, adjust `top: -15%; left: -15%; width: 130%; height: 130%;` on the background layer.
- If the window does not feel like a "window," check that `overflow: hidden` is applied.

## Browser support

- Chrome / Firefox / Safari (desktop)
- iOS / Android (touch events)
- No external libraries required

## Output expectations

Prefer a single HTML file with:

- three-layer structure
- mouse and touch drag support
- double-click reset
- adjustable `MAX_ANGLE` and `translateZ` constants
- clean, minimal styling
- console logs for debugging
