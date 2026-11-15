# Motion and performance

> The top-tier bar: the most beautiful, most premium result, and smooth on fast and slow computers, fast and slow phones alike. Smoothness is part of the design, not an optimisation pass before launch.

## Restraint in motion

- **Motion serves state changes only**: appear, expand, complete, fail. Avoid purely decorative infinite loops (shimmer, breathing glows, pulsing dots) wherever you can. If one is truly needed, pause it when it leaves the viewport.
- **One set of easings**:
  - Regular transitions use `--ease: cubic-bezier(.28,.11,.32,1)`, Apple's "fast start, slow settle";
  - Entrances use `--ease-out: cubic-bezier(0,0,.5,1)`;
  - When a little spring is needed, use `--spring: cubic-bezier(.2,.9,.25,1.02)`, which overshoots by only 2%. No jelly.
- **Durations**: buttons and hover 150–250ms; card expansion and modals 300–450ms; scroll-in 600–900ms. Animations longer than 1 second are reserved for moments worth celebrating, such as completion.
- **Scroll-in**: a small rise (16–24px) plus a fade, played once. Don't replay it every time the user scrolls back.
- **Hover**: cards lift 2–4px, and the shadow transitions from `--sh-card` to `--sh-card-h`. Images may scale to 1.02–1.04, no more.
- **Press**: `transform: scale(.985)`, so it feels pressed.
- **Don't**: large fog or glow fields, halos, decorative horizon lines or rules, or motion backgrounds unrelated to the content.

## Long-task progress (e.g. redeem, top-up, generate)

- **The button grows into a card**: on click, the button morphs in place into a progress card (spinner → expand → status line). Don't open a new overlay. Use the same unit for the sizes before and after the morph (px for both, for example), or the transition will jump.
- **Progress must be real**: compute it from a real clock. If the user switches to another tab and comes back, progress keeps moving. Never fake progress with animation duration.
- **Stages**: split the process into 3–4 named stages, each rotating one or two short sub-step lines beneath it. When the wait runs longer than usual, swap in a reassuring line, such as "Taking a little longer than usual, still working."
- **Draw the progress onto the brand ("engraving")**: more recognisable than a spinner or a progress bar is making the progress itself the signature visual. For example, the product mark first appears as a faint outline, and a glowing pen engraves it stroke by stroke along the contour; how far the pen has gone is how far the progress has gone. The pen tip throws off a few sparks and brightens gently with a heartbeat at key stages. Split a multi-part outline into several paths and engrave them in order; with one shared dash pattern, every segment resets and the pen tip no longer lines up.
- **Tier colours**: one animation for every tier, with only the accent colour changing (for example, teal for entry, ice blue for mid, purple for premium, champagne gold for top), written as CSS variables on the card. The skeleton stays the same.
- **Completion**: it should glow, not explode. The pen tip turns into light and spreads outward along the finished lines, with a little light dust and one soft ripple, then breathes gently. No solid colour fills, central spark bursts or hard-edged white rings.
- **Failure**: the drawn lines retract, colours dim, the element shakes slightly, and one sentence states the consequence, such as "Your credit has been returned."

## Tier heavy effects by device

Particles, WebGL and shaders look free on a good computer, but on software rendering, low-core CPUs and old phones they drag the frame rate below 20 fps. Always use three tiers:

| Tier | Behaviour |
|---|---|
| 0 | As designed |
| 1 | Half the particles, capped at about 30 fps, no glow, DPR fixed at 1 |
| 2 | Static image |

**Starting tier**: start directly at tier 1 when WebGL is unavailable, the renderer is a software implementation, the CPU has ≤ 2 cores, or memory is ≤ 2GB.

**Runtime check**: take the median frame interval and drop a tier if it is too slow. **Only ever step down, never up**, so it never flips back and forth between tiers.

```js
// Starting tier
function startTier() {
  try {
    const c = document.createElement('canvas');
    const gl = c.getContext('webgl') || c.getContext('experimental-webgl');
    if (!gl) return 1;
    const ext = gl.getExtension('WEBGL_debug_renderer_info');
    const r = ext ? String(gl.getParameter(ext.UNMASKED_RENDERER_WEBGL)) : '';
    if (/swiftshader|llvmpipe|basic render|software/i.test(r)) return 1;
  } catch (e) { return 1; }
  if ((navigator.hardwareConcurrency || 8) <= 2) return 1;
  if ((navigator.deviceMemory || 8) <= 2) return 1;
  if (matchMedia('(prefers-reduced-motion: reduce)').matches) return 2;
  return 0;
}

// At runtime: from 1 second after the animation starts, take the median frame interval every 40 frames; only step down, never up
function makeTierWatch(getTier, setTier) {
  const buf = []; let last = 0; let warm = performance.now() + 1000;
  return function onFrame(now) {
    if (last && now > warm) {
      buf.push(now - last);
      if (buf.length >= 40) {
        const m = buf.sort((a, b) => a - b)[20]; buf.length = 0;
        const t = getTier();
        if (t === 0 && m > 24) setTier(1);
        else if (t === 1 && m > 45) setTier(2);
      }
    }
    last = now;
  };
}
```

Write the current tier onto the canvas, for example `canvas.dataset.lite = tier`, to make debugging and review easier.

## Required power savings

- Observe effects with `IntersectionObserver`: `cancelAnimationFrame` when they leave the viewport, and resume when they come back.
- `document.visibilitychange`: pause when the tab is hidden.
- `@media (prefers-reduced-motion: reduce)`: turn off entrance and looping animations, keeping only the state changes themselves.
- **Frosted glass** (`backdrop-filter`) only on small elements such as the nav. A full-screen blur runs at only 30-odd fps under software rendering.
- **Images**: use `fetchpriority="high"` for hero images, and add `loading="lazy"` and `decoding="async"` to all other images. Always set `width`/`height` to prevent layout shift while loading.
- **Fonts**: on Apple devices, use the system SF and PingFang directly, with no font download. On other devices, load Chinese fonts on demand in `unicode-range` slices, with `font-display: swap`.

## How to test

Don't just look on your development machine. Use the Chrome DevTools Protocol (or the Performance panel) to simulate:

- 4× CPU throttling;
- `--disable-gpu` (to simulate software rendering);
- a 390-wide phone viewport at DPR 3, with 6× CPU throttling.

Record the hero's frame rate and main-thread usage. Target: no lower than 50 fps on weak devices, or at least an automatic drop to a tier that runs smoothly.
