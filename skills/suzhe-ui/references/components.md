# Components

Every component builds on the variables and classes in `tokens.css`. This file covers structure, key values and common mistakes only, not full pages (for a full page, see `assets/starter.html`).

## Contents
1. Top navigation
2. Hero
3. Section header and two-tone headline
4. Cards: plan cards, product cards, reason cards, showcase tiles
5. Brand banners
6. Horizontal shelf
7. Modals and mobile bottom sheets
8. Forms: inputs, steps, errors
9. Feedback: alerts, toasts, in-button loading
10. Long-task status card
11. Badges, selectors, segmented controls
12. Footer
13. Empty states
14. Icons

---

## 1. Top navigation

```html
<nav class="gnav" aria-label="Main">
  <div class="gnav-in">
    <a class="brand" href="/">…logo + name…</a>
    <div class="gnav-links">…links, flex:1, centred…</div>
    <div class="gnav-act">…36px round icon buttons / 28px-tall light pill sign-in button…</div>
  </div>
</nav>
```

- 48px tall, translucent dark `rgba(22,22,23,.86)` with `saturate(180%) blur(20px)`. Links 12.5px, white at 80% opacity, pure white on hover.
- The logo is a single-colour SVG on transparency (`fill: currentColor`), about 20×13px, followed by the name at 14px/600. **Never put the logo in a rounded square.**
- Dropdown menus span the full width: dark at `.96` opacity, content in three columns, large links at 600 24px; the rest of the page sits under a `rgba(0,0,0,.24)` + `blur(18px)` scrim.
- Open and closed state lives in attributes (`[aria-expanded="true"]`, `[data-menu-open]`). Styles follow the attributes, not classes added by JS.
- Below 1023px, switch to a hamburger: two 16×1.4px lines that rotate ±45° into a × when open. The menu covers the full screen, links at 600 28px, appearing one by one with a 30ms stagger.
- The first section must leave room for the nav: `padding-top: calc(var(--nav-h) + …)`.

## 2. Hero

```html
<section class="hero sec-black glow-host">
  <h1 class="h-hero">The value, in one sentence</h1>
  <p class="sub">One supporting line, two lines at most</p>
  <div class="hero-cta"><a class="btn btn-lg btn-primary">Primary action</a><a class="tlink">Secondary action</a></div>
  <div class="hero-stage"><img …signature visual…></div>
</section>
```

- **Height**: `max(440px, calc(100svh - var(--head)))` (with a `100vh` line before it as a fallback). `--head` is how much of the next section shows: about 120px on desktop, about 200px on phones. **The next section's title should be visible at the bottom of the hero**, so people know there is more below.
- **Structure**: centred headline → button group (gap 16px, 28px above) → signature visual stage (`flex: 1 1 0` takes the remaining height; images scale by height and are cropped at the sides when wider than the viewport).
- **Background**: black, overlaid with two very faint radial gradients (opacity .07–.09) for a shift in colour temperature. No background image:
  ```css
  background:radial-gradient(56% 60% at 12% 0%,rgba(46,211,166,.075),transparent 70%),
             radial-gradient(48% 56% at 92% 6%,rgba(90,125,255,.085),transparent 70%),#000;
  ```
- **Entrance**: headline `rise 1.1s var(--spring)`, button group delayed .1s, stage `fade 1.2s` delayed .15s.
- **Short screens**: two steps, `(max-height:820px)` and `(max-height:700px)`, progressively shrink the headline and spacing to give the height to the signature visual.
- **The hero is the only place on the page allowed a looping animation**, and it pauses when it leaves the viewport.

## 3. Section header and two-tone headline

```html
<section class="sec sec-gray">
  <div class="wrap">
    <header class="sec-head">
      <p class="eyebrow">New</p>
      <h2 class="h-sec"><span>Main clause: what it is. </span><span class="t-soft">Second clause: why it's worth it.</span></h2>
    </header>
    …content…
  </div>
</section>
```

- One headline covers both *what* and *why*, with the second clause in secondary grey. This is the Apple Store headline pattern: dense with information without feeling crowded.
- Sections are left-aligned (only the hero and promo banners are centred).
- A section may end with a "Learn more ›" text link, never another button.

## 4. Cards

**The ground decides how a card is drawn**: white cards with `--sh-card` on grey sections; grey cards (`--bg-2`) with no shadow on white sections. `.card` in `tokens.css` already follows this rule.

### Plan cards (subscription tiers, bundles)
- `padding: 30px 24px 24px; border-radius: 18px`.
- Top to bottom: product image (about 132px) → eyebrow → name (600 26px/1.12) → one-line tagline → price (600 28px/1, `tabular-nums`; unit 14px secondary grey; struck-through price 14px tertiary grey) → hairline + feature list → button. The button uses `margin-top:auto` to sit at the bottom, so buttons line up across cards.
- The feature list uses 16px round icons: a white check on black means included, blue means upgrade, grey means not included. Write list rows as `grid-template-columns: 16px minmax(0,1fr)`.
- Hover: the card lifts 3px and the shadow deepens; the product image goes `scale(1.05) rotate(-2deg)`, .7s spring.
- 4 columns on desktop (3 columns for 3 tiers), 2 on tablet. **On phones, switch to a horizontal card strip** (`flex: 0 0 min(82vw,320px)` + snapping) and drop the hover lift.

### Product cards
- 248px wide, at least 344px tall, 22px padding. Name 600 19px/1.26, truncated after two lines.
- **The product image is the signature visual**: a render on transparency with `filter: drop-shadow(0 14px 16px rgba(0,0,0,.16))`. Behind it sits a soft glow whose colour comes from each card's own `--glow` variable:
  ```css
  .card .media{position:relative}
  .card .media::before{content:"";position:absolute;inset:16% 20%;border-radius:50%;
    background:radial-gradient(closest-side,var(--glow),transparent);filter:blur(16px);opacity:.75;z-index:-1}
  ```
- A featured product may use a wide card: text left, image right (56% / 44%), optionally on black. On phones, image on top, text below.

### Reason cards (why choose us)
- Grey cards, `scale(1.015)` on hover. A 40px line icon sits on top; the title uses `margin: auto 0 8px` to push to the bottom of the card, leaving breathing room above.
- 3 columns on desktop, 2 on tablet, 1 on phones, laid out in a row: icon left, text right.

### Showcase tiles (featured, flagship)
- Two-column tiles, minimum height `clamp(560px,46vw,680px)`, content centred, on grey or black, **no corner radius**, separated by a 12px white seam.
- On hover, the image goes `translateY(-8px) scale(1.035)`, 1s spring.

## 5. Brand banners

- The skeleton stays: backgrounds are black, white or light grey only; type is the system sans; buttons are the site's own pill buttons.
- Brand character lives only in accents: the eyebrow colour, one gradient word in the headline (`background-clip:text`), a soft brand-coloured glow at the bottom, a rounded brand tile in the style of an Apple app icon.
- Each banner declares a set of local variables on its own root selector (`--xx-glow`, `--xx-accent`). They apply only inside the banner and never leak into site-wide variables.
- Full-bleed banners are about 600px tall, with 132px / 136px vertical padding (92px / 96px on phones).
- Put a `.seam` (12px white seam) above coloured banners, like the white gaps between banners on Apple's homepage.
- Several banners on one page must deliberately differ in composition and material. Never reuse one template.
- Glows are oversized radial gradients on a `z-index:-1` pseudo-element, with `.glow-host` (`isolation:isolate`) on the parent.

## 6. Horizontal shelf

```css
.track{display:flex;gap:18px;overflow-x:auto;overscroll-behavior-x:contain;scroll-snap-type:x mandatory;
  scroll-padding-inline:var(--pad);padding-inline:var(--pad);scrollbar-width:none}
.track::-webkit-scrollbar{display:none}
.track>*{flex:0 0 auto;scroll-snap-align:start}
/* Full-bleed, but the first card lines up with the left edge of the content */
.track{--pad:max(var(--gut),calc((100% - var(--wrap))/2))}
```
- Edge fades use `mask-image` and follow state: the left fade shows only when the shelf can scroll left, the right fade only when it can scroll right.
- On desktop, left and right arrows may be shown, each scrolling `max(240px, visible width × .82)`. Hide them under `@media (hover:none)`.

## 7. Modals and mobile bottom sheets

- Scrim `rgba(0,0,0,.42)`, fading in over .24s.
- Box: `width:min(560px,100%); max-height:calc(100dvh - 48px); padding:30px 30px 28px; border-radius:22px; box-shadow:var(--sh-modal); overscroll-behavior:contain`.
- Entrance: `modal-in .44s var(--spring)`.
- Header row: title on the left at 600 24px/1.17, close button on the right. **The close button is a bare icon that brightens on hover** (round close buttons with an outline or a grey fill were rejected twice).
- **On phones, it becomes a bottom sheet**: pinned to the bottom, 16px top corners, `sheet-up .4s var(--ease-apple)`, `padding-bottom: calc(24px + env(safe-area-inset-bottom))`, and a 36×5px grabber at the top. Once the content is scrolled to the top, it can be dragged down to close: offset = finger offset × .85 (damping). Released past 120px, it closes; otherwise it springs back.
- **Open in two steps**: first add `.is-open` (`display:block`), then on the next frame (two `requestAnimationFrame`s) add `.is-in` to trigger the transition. Otherwise the transition won't play.
- **Focus**: on open, move focus into the modal (`focus({preventScroll:true})`); Tab cycles within the modal; Esc closes it (only the topmost one when stacked); on close, focus returns to the trigger. Set `aria-modal="true"`.
- **Scroll lock**: while open, `html, body { overflow:hidden }`.

## 8. Forms

- **Inputs**: use `.field` from `tokens.css`. 52px tall, 12px radius, 17px text. Focus is a single 2px blue border (`border-color` plus a same-colour `0 0 0 1px` shadow), with no outer glow.
- **Field header**: label on the left (600 14px), hint on the right (13px tertiary grey, at most 62% of the width, single line with ellipsis). On phones, the hint wraps to the next line.
- **Errors**: driven by `aria-invalid="true"`. The border turns red and shakes once; below it appears a 13px error line with a 15px line icon, set to `role="alert"`. **Show errors only after submit.**
- **Autofill** background fix: `:-webkit-autofill { box-shadow: inset 0 0 0 100px <background>; -webkit-text-fill-color: <text colour> }`.
- **Password reveal**: a 40px button at the right edge of the input (give the input `padding-right:48px` to make room). The slash is drawn with `stroke-dasharray`, and the state lives in `aria-pressed`.
- **Verification code input**: 48px tall, 600 20px text, `letter-spacing:.32em`, centred, `tabular-nums`.
- **Steps**: on desktop, a left column of 28px numbered circles joined by a 2px vertical line; a completed step's circle turns blue with a white check. On phones, drop the number column and put the number in a 24px circle before the title.
- **The primary button on flow pages** may be graphite (`.btn-ink`), 10–12px radius, 44–50px tall. Brand pages use the blue pill button.

## 9. Feedback

- **alert**: 14px radius, 14px/1.55 text, with a pale background per state (error / success / info / default grey). The 18px round icon on the left is drawn with `::before` and inline SVG, no extra DOM. The entrance moves up only 6px.
- **toast**: use `.toast` from `tokens.css`. A translucent dark pill, centred at the top just below the nav, gone after 3 seconds. The container gets `role="status" aria-live="polite"`.
- **Copy feedback**: after copying, the icon or text turns green for about 1.5 seconds. No interrupting dialog.
- **In-button loading**: an 18px spinner absolutely positioned at the centre of the button. The label goes to `opacity:0` but keeps its space, so the button width doesn't jump. On success, the button turns green and stops accepting clicks.

## 10. Long-task status card (redeem, top-up, generate)

Full details are in "Long-task progress" in `motion-perf.md`. Key points:

- The primary button grows in place into a dark status card (similar to the Dynamic Island). The staged timeline is roughly: 0ms the icon becomes a spinner → 650ms the secondary button collapses → 950ms the card expands horizontally and vertically → 1620ms content enters → 1900ms the segmented steps unfold.
- Card content: a real progress ring (`stroke-dashoffset` bound to a progress variable) + a highlight flowing along the ring + a 4-segment bar below (the current segment shimmers, completed segments fill).
- Animate height with `grid-template-rows: 0fr → 1fr`. Don't hard-code `max-height`.
- Collapse by playing the expansion in reverse: first squash into a wide pill, then narrow back into the button.
- Success: a badge pops in and the check mark draws itself. Failure: the progress ring shakes, the product image turns grey, and one sentence states the consequence.

## 11. Badges, selectors, segmented controls

- **Status badge**: `padding:5px 10px`, pill-shaped, 600 12px/1, a pale background with darker text in the same hue.
- **Status dot**: an 8px dot with a 3px pale halo in the same colour. **Normal states carry no dot; only anomalies do** (orange when stock is low, red when it's out).
- **Live pill**: pale blue background `rgba(0,113,227,.08)`, text `#0058b0`, 500 12px, with a 6px blinking dot in front and numbers in `tabular-nums`.
- **Selectable options** (pills or cards): `inset 0 0 0 1px var(--line)` by default, a darker outline on hover, and a pale blue background plus `inset 0 0 0 2px var(--blue)` when selected. Drawing the outline with an inset shadow keeps the size from jumping on selection.
- **Segmented control**:
  ```css
  .seg{display:inline-flex;gap:2px;padding:2px;border-radius:9px;background:var(--bg-3)}
  .seg button{padding:7px 16px;border:0;border-radius:7px;background:none;font:500 13px/1.3 var(--f-text)}
  .seg [aria-pressed="true"]{background:#fff;box-shadow:0 1px 3px rgba(0,0,0,.12),0 0 0 .5px rgba(0,0,0,.04)}
  ```
- **Sidebar selection indicator**: a raised white card sits behind the selected item and slides to the new one on change, animating `transform` and size over .5s with `var(--ease-glide)`.

## 12. Footer

- Grey background, 12px/1.4, secondary grey.
- Structure: notes → hairline → 4 columns of links (column headings 600 12px in the primary text colour; links `--ink-4`, turning primary with an underline on hover) → hairline → copyright and language switcher (separated by 1px vertical rules, current item in bold).
- On phones, links go to 2 columns, and copyright and language stack vertically.

## 13. Empty states

- One centred line of secondary-grey text with 48px of space above and below, followed by a clickable alternative (for example, "Try A, B or C"). No large illustrations.
- If the data can be inlined into the page, inline it so the first frame renders. No skeleton screens needed.

## 14. Icons

- Use line SVGs: `fill:none; stroke:currentColor; stroke-width:1.5–2; stroke-linecap:round; stroke-linejoin:round`, so the colour follows the text.
- Arrows, checks and play triangles are easy to draw with CSS borders.
- No icon fonts, no emoji as icons.
- **When a button contains an SVG icon, write click delegation as `e.target instanceof Element`**, not `HTMLElement`.
