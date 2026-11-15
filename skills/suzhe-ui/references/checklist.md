# Engineering details and pre-ship checks

## 1. Pitfalls to avoid while coding

Every one of these has caused a real problem in production, and **none of them can be caught from screenshots alone**.

| Pitfall | Symptom | Fix |
|---|---|---|
| iOS input zoom | Tapping an input on a phone zooms the whole page in, and it won't zoom back out | On mobile, `input, textarea, select { font-size: 16px }` (≥16px prevents the zoom) |
| grid/flex children overflowing | One long unbroken URL widens a `1fr` track, and the whole page scrolls horizontally | Add `min-width: 0` to grid/flex children; add `overflow-wrap: anywhere` to long text |
| Clicks on SVG icons do nothing | With an SVG icon inside a button, clicking the icon makes `e.target` an `SVGElement`, and a delegate that checks `instanceof HTMLElement` returns early | Always write `const t = e.target instanceof Element ? e.target : null; t?.closest('…')` |
| Inter turns 5x5 into 5×5 | Inter's `calt` feature replaces an x between digits with a multiplication sign | `font-feature-settings: "calt" 0`. Note that the `font` shorthand resets this feature, so set it again after the shorthand |
| Jumping numbers | Countdowns, prices and progress percentages jitter in width as they change | `font-variant-numeric: tabular-nums` |
| Autofill fires no events | Browser password autofill doesn't fire an `input` event, so buttons that enable on input stay grey | Besides `input`, also listen for `change` and `animationstart` (paired with `:-webkit-autofill`), or poll every 400ms as a fallback |
| Removing an element breaks all the JS | A DOM element was removed, but a bare top-level `$('x').addEventListener` remains in the module. It throws and aborts all the initialisation after it. The page looks fine, but nothing responds to clicks any more | Before removing an element, search globally for its id and classes; always check the console for errors afterwards |
| Browsers caching old assets | CSS/JS was changed, but users still see the old version | Version static asset URLs as `?v=<date>-<n>`. Use a new number on every change and update every reference at once; never reuse an old number |
| Search box gets autofilled | The browser fills the username into a search box | `autocomplete="off"`, plus a random suffix on `name` if needed |
| Hidden iframes get throttled | A bot-check iframe hidden with `display:none` is throttled by the browser, and the check never returns a result | Hide it with `opacity:0; pointer-events:none; height:0; overflow:hidden` plus `inert`, not with `display:none` or `visibility:hidden` |
| Distorted global focus ring | A global `:focus-visible` that sets `border-radius` turns the focus ring of pill buttons square | Global focus styles set only `outline`; let each element keep its own radius |
| Button styles overridden | Higher-specificity rules in dark sections or the nav, such as `.dark a { color }` or `html.dark button:not(...)`, override button text colour (for example, white text on a light grey button, nearly invisible) | Write link rules inside sections as `a:not(.btn)`; check for global `a` / `button` rules before styling buttons; don't patch with `!important` |
| Smooth scrolling interferes with tests | `html { scroll-behavior: smooth }` keeps a scripted `scrollTo` from landing immediately | In tests, use `scrollTo({ top, behavior: 'instant' })` |

## 2. Pre-ship checks (in order)

1. **Phone width**: at 390×844, the page has no horizontal scroll (`document.documentElement.scrollWidth <= innerWidth`), no text overflows, and buttons are tappable (hit area ≥ 44px).
2. **Section backgrounds**: read out each section's background colour and confirm that neighbours differ. Don't rely on your eyes.

   ```js
   [...document.querySelectorAll('main > section, body > section')]
     .filter(s => s.offsetHeight > 0)
     .map(s => [s.id || s.className, getComputedStyle(s).backgroundColor])
   ```

3. **Console**: 0 errors (including errors from requests blocked by security headers).
4. **Real clicks**: for buttons with icons, click the centre of the icon at real coordinates, not just with JS `.click()` (its target is the button itself, so it can't catch the SVG problem).
5. **Keyboard**: Tab through the page. Focus is always visible, it can't escape a modal, and Esc closes the modal.
6. **Light and dark**: if the page supports dark mode, take screenshots of both. For pages designed only in light, confirm the browser doesn't invert them on a system set to dark.
7. **Weak devices**: for pages with animation or effects, simulate weak devices as described in `motion-perf.md`, and confirm the tier drops and nothing stutters.
8. **Reduced motion**: with `prefers-reduced-motion` turned on, all looping animations stop and the page remains complete and usable.
9. **External dependencies**: for pages that use bot checks, payments or third-party sign-in, open them for real once and confirm they load. Security hardening must never come at the cost of functionality.
10. **Copy**: count the elements and sentences, and delete everything you can. Does the help text say it in one sentence? Are any greyed-out or unavailable items still showing?
