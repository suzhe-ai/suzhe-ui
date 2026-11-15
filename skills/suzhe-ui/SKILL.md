---
name: suzhe-ui
license: Apache-2.0
description: Suzhe UI — the web design method behind suzhe.ai. Two registers on one restrained skeleton (a commerce register with white/#f5f5f7/black sections, system fonts and a single blue pill accent; an editorial register with warm paper, serif display type and ink buttons), one signature visual per page, and nothing that doesn't earn its place — smooth on every device. Use this skill whenever the user builds or redesigns any web UI: landing pages, stores and product pages, sign-in/sign-up, checkout, redeem/activation flows, announcements, docs, dashboards or share cards — or asks to make something "premium", "high-end", "Apple-like", "listed-company grade", "less cluttered", even if Suzhe is never mentioned. 苏哲 UI：做或改任何网页、商店、登录、结账、文档、后台，或要求「高级」「有质感」「上市公司水准」「太乱了」时使用。
---

# Suzhe UI

**Restrained skeleton. One signature visual. Fewer elements. Smooth on every device.**

Premium is not the sum of effects. It comes from four things:

1. **A restrained, consistent skeleton** — people trust it at a glance.
2. **One signature visual per page** — the page is recognisable, not a clone of someone's homepage.
3. **Removing everything that doesn't earn its place** — calm to look at, effortless to use.
4. **Smoothness as part of the design** — the most beautiful page is only top-tier if it also runs smoothly on a cheap phone and an old laptop.

Every rule below comes from real redesign rounds on a live site. Rejected attempts and why they failed are in `references/rejected.md`.

The instructions are written in English; write page copy in the user's language. For Chinese pages, see the CJK typography rules in §1.

---

## 0. Before you start: page type and register

| Page type | Examples | Register | Guide |
|---|---|---|---|
| Brand / showcase | home, store, product, campaign | Commerce | §1–§4 |
| Identity moment | sign-in, sign-up, about, share card, brand statement | Editorial | `references/page-types.md` (D) |
| Formal | notices, terms, surveys, help & API docs | Docs | `references/page-types.md` (B) |
| Tool | mail, admin, console | Tool | `references/page-types.md` (C) |
| Flow | checkout, redeem, forms | Commerce | `references/page-types.md` (D) |

Showing off on a terms page looks unserious; reskinning a tool fixes nothing; a checkout dressed as a marketing page makes people nervous; a sign-in page in shop colours feels cold. **One page, one register.** Registers share spacing, motion and focus rules.

## 1. Skeleton

Copy `references/tokens.css` into the project first; every value below is in it.

### Commerce register (default)
- **Three backgrounds only**: white `#fff`, light grey `#f5f5f7`, black `#000`. **Adjacent sections must differ** — sections are separated by background, not by borders or rules. White and `#f5f5f7` are nearly indistinguishable in a thumbnail, so verify with code (`references/checklist.md`).
- **Text is greyscale**: `#1d1d1f` primary, `#6e6e73` secondary, `#86868b` captions; on black use `#f5f5f7` and `#a1a1a6`. Never pure black text.
- **One accent colour**: blue `#0071e3`, only for primary buttons, links (`#0066cc`) and focus rings. One warm eyebrow colour (`#b64400`) may mark a label.
- **System font stack**: SF / PingFang on Apple devices (no download), Inter + Noto Sans SC elsewhere. Headlines weight 600; Latin headlines get tighter tracking as they grow (−0.015 to −0.03em); **CJK tracking stays 0**. Body 17px / 1.47. Hierarchy comes from size, weight and grey — not boxes. Never bundle commercial fonts.
- **Two-tone headline**: first clause in ink says *what*; second clause in grey says *why*.
- **Pill buttons** (`border-radius: 980px`): one blue primary per section; secondary is an outline pill or a "Learn more ›" text link.
- **Cards depend on the ground**: white cards with a faint shadow on grey sections; grey cards without shadow on white sections. Radius 18px, lift 3px on hover.
- **Frosted glass only for things floating above content**: nav, bottom purchase bar, toast. Never cards or sections.
- **Dimensions**: content max 1120px, gutters 22px (20px on phones); section padding 96/104px (64/72px on phones). The hero is the viewport height minus ~120px so the next section's title peeks in; full-bleed banners about 600px tall. Breakpoints match apple.com: 734px and 1068px.
- **Third-party brand colour is an accent only**: eyebrow colour, one gradient word, a soft glow, an app-icon-style tile. Never a full-bleed brand fill or a sudden serif inside a commerce page — it looks pasted in from another site. Leave a 12px white seam between coloured banners.

### Editorial register (identity moments)
Add `class="editorial"` to the page root.
- Warm black `#141413` by default, warm paper `#faf9f5` when the system is light; inline the background in `<head>` to avoid a white flash.
- **Serif display type** for the one big statement (weight 500, `clamp(40px, 4.2vw, 62px)`, `text-wrap: balance`); everything else stays in the system sans. Subset the serif to the glyphs on the page.
- **Ink buttons, not blue**: the primary button inverts the text colour (black on paper, white on warm black), radius 10px, 44px tall.
- Thin 1px borders, radius 24px cards, no heavy shadows. One warm accent at most once per page.

## 2. Signature (not an apple.com clone)

A skeleton alone reads as "an Apple clone, but cheaper". The signature visual is what people remember.

- **Pick one visual that belongs to this product and this task.** Ask: what is unique about the product, and what must the user get done here? Grow the signature from those two answers. Directions — inspiration only, **never copy them**, or every site's sign-in page will look the same:
  - sign-in: one sentence the brand says to the user, next to a short film of the real product;
  - store hero: the product itself, rendered in 3D, or a physical object that turns or switches;
  - redeem / long task: the progress drawn onto the brand mark itself (see `references/motion-perf.md`);
  - docs: the typography is the signature.
- **Never reuse a signature** on two pages; it reads as laziness.
- **Texture comes from material and light**: brushed metal, grain, layered shadows, iridescent edges, cursor-following highlights, 3D product renders — not white cards plus flat blue buttons.
- **Several brand banners on one page**: each differs in composition and material inside the skeleton. Start from how that brand presents itself.
- **Don't**: large fog or glow fields, decorative horizon lines, motion backgrounds unrelated to the content.

## 3. Less (content and elements)

- **Hide what is unavailable.** Out-of-stock, retired or forbidden items are not shown; hide the whole group if it is empty. Greyed-out cards make a page look messy.
- **Help text is one sentence**: what to open, what to copy, with the entry point visible and clickable. No unsolicited tutorial cards, FAQs or diagrams. Only a genuinely branching flow earns a small flow chart with short node labels.
- **Result pages show the result**, nothing else. Every extra sentence is one more thing to misread; rules belong in the terms.
- **State positives** ("includes X"), not stacks of negatives and disclaimers.
- **One step if one step is possible**: let people pick directly instead of "enter a mode, then pick".
- **Don't surface numbers that cause hesitation** in copy, such as fee percentages or internal processes.

## 4. Timing (interaction sequencing)

- **Hints appear only when needed.** Validation errors appear after submit, next to the field. Bot checks run silently in the background, appear once the main input is filled, and prompt only if the user actually missed them. Never scare people early or make them wait.
- **Long tasks show real progress**: named stages, computed from a real clock so it keeps moving while the tab is hidden. Swap in a reassuring line when it takes longer than usual.
- **Completion should glow, not explode.** Failure states its consequence ("your credit has been returned"), not just a red error.
- **Motion marks state changes only**: appear, expand, complete, fail. Default easing `cubic-bezier(.28,.11,.32,1)`; animate only `transform` and `opacity`. **At most one looping animation per page**, paused off-screen. Heavy effects degrade by device tier; respect `prefers-reduced-motion`. See `references/motion-perf.md`.
- **Normal states carry no marker; only anomalies do**: no dot when stock is fine, orange when low (and hide it entirely when out, per §3).

## 5. Consistency (many pages, many sub-sites)

- **Brand tokens are site-wide**: logo, fonts, focus colour, favicon. Layouts, registers and signatures may differ; tokens may not.
- **The logo is a single-colour mark on transparency**, inverted in dark mode. **Never put it in a rounded square** — in dark mode it becomes a white tile with a black glyph.
- **Pages with both themes follow the system by default** and remember a manual choice. Pages designed only in light do not get a forced dark branch.
- "Unify the visuals" means unify tokens, not force every page into one layout.
- **Improve incrementally.** Never rebuild a page people already find comfortable in one go; change one section at a time, ship it, verify it. Big redesigns start as a local preview with screenshots.

## 6. Workflow

1. Decide the page type and register (§0).
2. Name the signature in one sentence: what it is and how it relates to the task.
3. Lay out the sections and their backgrounds; check that neighbours differ.
4. Paste `references/tokens.css`; build components from `references/components.md`.
5. Cut: for every element and every sentence ask "what would the user lose without it?" If there is no answer, delete it.
6. Verify with `references/checklist.md`: no horizontal scroll at 390px, neighbouring backgrounds differ, zero console errors, visible keyboard focus, smooth on a throttled CPU.

## References (read as needed)

| File | When |
|---|---|
| `references/tokens.css` | Always — tokens and base styles for both registers, ready to paste |
| `references/components.md` | Nav, hero, sections, cards, buttons, forms, modals, toasts, footer |
| `references/page-types.md` | Formal, tool, flow and identity pages |
| `references/motion-perf.md` | Motion, long-task progress, WebGL/particle effects, device tiers |
| `references/rejected.md` | Before any redesign — 16 approaches that were rejected and why |
| `references/checklist.md` | Engineering pitfalls while coding; the pre-ship checklist |
| `assets/starter.html` | Starting a brand page from scratch |
