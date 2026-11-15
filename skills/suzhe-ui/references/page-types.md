# Four page types, four approaches

Decide which type of page you are building before you start. Different pages on one site can take different approaches, but **design tokens (fonts, focus blue, logo) stay consistent**.

| Type | Typical pages | Character | Keywords |
|---|---|---|---|
| A. Brand / showcase page | Home, store, product, campaign | Striking, restrained | Alternating black, white and grey; large headlines; products as the signature visual |
| B. Formal page | Notices, terms, privacy policy, surveys, help docs, API docs | Trustworthy | Solid backgrounds, document typography, hairline tables |
| C. Tool page | Mail, admin, console, data lists | Efficient | White background, information density, search and filters |
| D. Flow page | Sign-in, sign-up, checkout, redeem, forms | Focused, reassuring | One task, few words, clear states |

**Two registers**: type A pages, checkout and redeem use the **commerce register** (white / light grey / black, system sans, blue pill buttons). Identity moments such as sign-in and sign-up, about pages, share cards and brand statements use the **editorial register** (warm paper or warm black, serif display type, ink buttons, thin borders). One page, one register; both registers share spacing, motion and focus rules.

---

## A. Brand / showcase pages

This is the core of the skill. See the "Skeleton" and "Signature" sections of `SKILL.md`, and `components.md` for how to build components.

**Page rhythm** (a reference structure; add or remove as needed):

```
Nav (48px, frosted glass, sticky)
Hero (black, or white with a strong key visual; about 600px tall, shorter on phones)
Section 1 (light grey #f5f5f7)
Section 2 (white #fff)
Full-bleed banner (black + a touch of brand-coloured glow)
Section 3 (light grey)
…
Footer (light grey, 12px small text)
```

- Every section: eyebrow (optional, 12–14px, accent colour) → headline (the value in one sentence) → one-line subhead → content (card grid or horizontal shelf) → a "Learn more ›" text link.
- Card grids: 4 or 3 columns on desktop, 2 on tablet, 1 or a horizontal scroll on phones. 18–22px between cards.
- Product cards: image on top (large radius, generous whitespace), then name, one selling point, price and button. Prices use `tabular-nums`.
- Unavailable products are not shown.

## B. Formal pages (document style)

**Principle**: trustworthy > pretty. No motion backgrounds, glass effects, glows or big gradient numbers.

**Design tokens** (light and dark sets; follow the system by default, and remember the user's manual switch in localStorage):

```css
:root{
  --bg:#fff; --surface:#f8f9fa; --ink:#202326; --text:#51565c; --muted:#6c7278;
  --line:#e9ebed; --line-strong:#dadddf;
  --accent:#476951; --accent-soft:#edf4ee;        /* A sober deep green, only for eyebrows, links and the current item */
  --code-bg:#f6f7f8;
  --sans:Inter,-apple-system,BlinkMacSystemFont,"Segoe UI","PingFang SC","Microsoft YaHei",sans-serif;
  --mono:"SFMono-Regular",Consolas,"Liberation Mono",monospace;
  --ease:cubic-bezier(.23,1,.32,1);
}
:root[data-theme=dark]{
  --bg:#151618; --surface:#1d1f21; --ink:#ededee; --text:#b4b8bc; --muted:#8d9399;
  --line:#2b2e31; --line-strong:#3b4044; --accent:#a4c3aa; --accent-soft:#25322a; --code-bg:#1c1e21;
}
```

**Typography**:

| Element | Spec |
|---|---|
| Body | 14px / 1.75, colour `--text` |
| Lead | 15px / 1.9, max width 680px |
| Eyebrow | 12px, weight 550, `--accent` |
| h1 | 34px / 1.3, weight 650, tracking -1px, `text-wrap: balance` (30px on phones) |
| h2 | 22px / 1.45, weight 620, tracking -.4px, 44px top margin |
| h3 | 18px / 1.5, weight 620 |
| Meta line | 10px monospace, tracking .05em, separated by vertical rules (date, version, reading time) |
| Tables | 12px / 1.8, 1px `--line-strong` outer border, 8px radius, header on `--surface`, cell padding 12px 15px |
| Inline code | .86em monospace, 1px hairline border, 4px radius |
| Callout | `--accent-soft` background, a circled "i" icon on the left, 12px / 1.9 |

**Layout**: three columns on desktop (left table of contents 256px / body up to 876px / right on-this-page outline 216px). Below 1200px, hide the right outline; below 680px, the left table of contents becomes a drawer. Top bar 72px, white, 1px bottom border, no frosted glass.

**Interaction**: headings show a # anchor on hover; `⌘K` opens a search dialog; code blocks have a copy button in the top-right corner, and a successful copy shows only a small toast (dark, centred at the bottom).

## C. Tool pages (Gmail / Google Account style)

**Principle**: a tool is good or bad because of its layout and interaction, not its colours. A redesign should change how fast people find things.

- White and light. No dark branch (unless the product itself calls for dark).
- Left navigation + top search box (large radius, light grey; turns white with a shadow on focus) + list body.
- List rows 40–48px tall, light grey on hover, pale blue when selected. Inline row actions appear only on hover.
- Higher density than brand pages: body text 13–14px, labels as small pills.
- Filters and categories stay visible, never hidden in submenus.
- Empty states: one sentence plus one primary action, no large illustrations.

## D. Flow pages

**Shared principles**: one screen, one task; one primary button; errors appear only after submit, right below the field at fault.

### Sign-in / sign-up (editorial register)
Sign-in is the user's identity moment with the brand, so it uses the editorial register (see `.editorial` in `tokens.css`), not the store's blue-pill skeleton.
- Two columns, wider on the left (about `1fr : .92fr`). On the left: one serif statement (what the brand says to the user, `clamp(40px,4.2vw,62px)`, weight 500, `text-wrap: balance`) + a one-line subhead + a form card (max 456px wide, 1px thin border, 24px radius, no heavy shadow). On the right: the product's own imagery (a short in-house film or the real interface, 28px radius), loaded only at 1024px and above. On phones, the whole block is neither shown nor downloaded.
- The primary button is ink (white with black text in the dark theme, black with white text in the light theme), 10px radius, 44px tall. Never blue.
- Follow the system theme by default: warm black `#141413` in dark, warm paper `#faf9f5` in light. Inline the background in `<head>` to prevent a white flash on the first frame.
- Subset the serif to the glyphs the page uses (a Chinese subset is a dozen-odd KB). Re-subset whenever the copy changes.
- The form advances in steps: email → password (or code), one question per step. Steps slide horizontally (forward enters from the right, back enters from the left).
- Bot check: it runs silently in the background as soon as the step opens, with the widget hidden at first. It surfaces naturally once the main input is filled, and a prompt appears only if it genuinely hasn't completed at submit.
- Terms are accepted by default and stated in one line of small text. No mandatory checkbox (unless the law requires one).

### Checkout / payment
- Keep it as a modal. Don't jump to a "brand payment page".
- Three things visible at a glance: amount, payment method (QR code or button), time remaining (countdown in `tabular-nums`).
- Put the order summary in a collapsible side card.
- Payment success feedback is instant and unambiguous, then moves on to the next step automatically.

### Redeem / activation / long tasks
- Product list on the left, action panel on the right (on phones, the product list becomes horizontal tabs at the top).
- A generous input that recognises and validates the format on paste. Help text is one sentence: what to open, what to copy.
- After submit, the button grows in place into a progress card (see "Long-task progress" in `motion-perf.md`).
- The result shows only the result itself, with no "suggested next steps".
