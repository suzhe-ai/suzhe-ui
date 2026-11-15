# Rejected approaches (counter-examples)

Every one of these was rejected during a real redesign. Read them before redesigning or building a new page. What they have in common is that they "look like they are trying hard", yet make the page feel cluttered, like someone else's site, or untrustworthy. Each entry covers **why it failed** and **how it was put right**.

---

## 1. Copying apple.com outright

- **Approach**: a 48px nav + a hero arc of 3D icons + white cards + blue pill buttons, the store homepage layout lifted wholesale for the sign-in page.
- **Verdict**: "It's obviously imitating Apple's website, but the result is just okay. I can't say exactly what's wrong, it just feels a bit cheap." "It overlaps with the main site."
- **Why**: users want premium **with an identity**, not a flat copy. The same key visual appearing on two pages reads as laziness.
- **Intermediate version**: on the left, a metallic "account card" on a dark stage, with the email being typed engraved on its face and the card tilting with the cursor; on the right, a white form. It had a signature visual, but it was still the store's cold skeleton.
- **Final version**: switched to the **editorial register**: a warm paper background (warm black in dark mode), one serif statement plus a thin-bordered form card on the left, the product's own short film on the right, and an ink primary button. Sign-in is the user's identity moment with the brand. It needs warmth and trust, not the bustle of a store.
- **Lesson**: design tokens (spacing, motion, focus rules) are consistent site-wide; **the register follows the page's role**. Pages that sell use the commerce register; identity moments use the editorial register. (All of this was this product's answer at the time, not a template.)

## 2. Full-bleed brand colour fills

- **Approach A**: a third-party brand banner with a "white-to-orange gradient + 3D image". Verdict: "The taste here is terrible."
- **Approach B**: a full terracotta fill + serif type + an oversized brand logo as the background. Verdict: "It doesn't go with the background."
- **Why**: the whole site uses three backgrounds: black, white and light grey. Drop in a block of brand colour or a set of serif type, and it looks cut out of someone else's site and pasted in.
- **Fix**: a black background with a touch of brand-coloured glow at the bottom, the system sans kept, the site's blue pill button kept, and the brand logo turned into a rounded tile like an Apple app icon. Brand character comes from accents only.

## 3. Reusing the previous banner's template

- **Approach**: a new product banner copied the composition of an existing one (centred headline + 3D lettering + product image), swapping only the product.
- **Verdict**: "Please don't make it the same as the last one. It's the same template."
- **Why**: when several banners on one page look the same, they lose their distinction and look cheap.
- **Fix**: first think about what the brand's own website looks like: illustration or photography, text left and image right or a centred composition, paper or metal. Then create the difference inside the site skeleton: background, type and buttons stay the same; composition, key image and accents change.

## 4. Showing off on formal pages

- **Approach**: a notice / poll page with an animated line background + glass cards + big gradient numbers.
- **Verdict**: "The layout right now is a joke." The request was for the formal layout of API docs and listed-company websites.
- **Why**: notices, terms, surveys and docs need to be **trustworthy**. Showing off looks unserious.
- **Fix**: a solid background, 14–15px body text, 1.75–1.9 line height, hairline tables, eyebrows in small accent-coloured type, light and dark themes. See "Formal pages" in `page-types.md`.

## 5. Tutorial-style help UI

- **Approach**: users didn't know which URL to visit for one step, so the answer was "three step cards + a mock page preview + one-click paste + a collapsible FAQ".
- **Verdict**: "It's way too complicated. It's a mess."
- **Why**: users were stuck for one reason only: **they didn't know which URL to open**. Putting the URL itself on the page solves it; everything else is noise.
- **Fix**: one sentence ("After signing in, open this URL, select all, copy, and paste below") + a URL box + an Open button. Invisible fault tolerance (such as automatically diagnosing a wrong paste) can stay, because it takes up no space.

## 6. Greying out sold-out or unavailable items

- **Approach**: sold-out products were greyed out and moved to the end, and fully sold-out categories sank to the bottom.
- **Verdict**: "If it's sold out, just don't show it. It's a mess."
- **Why**: a row of grey cards or a whole grey category drags down the quality of the entire page. Clean matters more than "letting people know it exists".
- **Fix**: if it's unavailable, don't show it. If a whole group is unavailable, hide the whole group.

## 7. Adjacent sections on the same background

- **Approach**: a new section copied the white background of the section above it, so the two white sections ran together.
- **Verdict**: "It doesn't look like it has any effect."
- **Why**: pages are divided into sections by alternating backgrounds. White (#fff) and light grey (#f5f5f7) are nearly indistinguishable in a thumbnail screenshot, so **you won't catch it from screenshots alone**.
- **Fix**: after adding, hiding or moving a section, read out each section's background colour in code and confirm that neighbours differ (see `checklist.md`).

## 8. Turning payment / checkout into a brand marketing page

- **Approach**: the lightweight checkout modal was rebuilt as a full "brand website–style" payment page (paper receipt, three-step progress, a stamp animation). Flawless engineering; every benchmark passed.
- **Verdict**: "Somehow it doesn't feel as comfortable as the current live version. It looks more like a brand's payment page." The whole proposal was then scrapped.
- **Why**: when paying, users just want to finish quickly and with confidence. Marketing-style hierarchy and heavy visuals add to the burden.
- **Fix**: keep the modal, with the QR code, countdown and order summary visible at a glance. If you need a reference, use Apple's checkout page: white, single column, generous whitespace, almost no decoration.

## 9. Reskinning a tool interface

- **Approach**: the redesign of a mail interface only changed the colours and tweaked the layout (white swapped for black).
- **Verdict**: "What on earth is this?" The request was for something "like Google's products, like a listed company".
- **Why**: a tool interface stands or falls on **layout and interaction**: can people quickly search, sort and find things? A reskin solves nothing.
- **Fix**: rebuilt on the layout and white, light style of Gmail and Google Account pages, focusing on search, categories and information density.

## 10. Hints that appear too early, or too late

- **Approach A**: as soon as the password step opened, it showed "Running a security check…" and the check widget. Verdict: "The user hasn't clicked anything yet. You can't just show 'please complete the security check' right away."
- **Approach B**: the check started only after "Continue" was clicked, and the widget popped up together with a warning. Verdict: "The logic is wrong."
- **Fix**: start the check silently in the background as soon as the step opens, with the widget hidden at first. It appears naturally once the main input is filled (most people pass silently and see nothing). Only if the user clicks submit and has genuinely missed the check does a prompt appear, with a slight shake.
- **Principle**: hints appear only when the user has actually missed something. Don't scare people early, and don't make them wait.

## 11. Dropping "follow the system" on hearing "make it all white"

- **Approach**: on hearing "white by default", several sub-sites that supported both light and dark were switched to a fixed light theme.
- **Result**: reverted the same day. The real requirement was **follow the system by default**, and remember the user's choice after a manual switch.
- **Exception**: brand pages and tool pages that were designed only in light (such as mail) should not get a forced dark branch.

## 12. "Helpful hints" on delivery or result pages

- **Approach**: a well-meant "suggested next step" was added below the delivery result.
- **Result**: the suggestion contradicted the rules in the product description and led to customer disputes.
- **Principle**: result pages show only the result itself. Rules belong in the product description and purchase terms, not scattered across pages.

## 13. Heavy hero effects without device tiers

- **Approach**: about 1,600 particles in the hero, drawn twice per frame, with blend modes and masks, running all the time.
- **Result**: 0.5ms per frame on a good computer, with no visible problem at all; but only 23 fps on a device with software rendering and a slow CPU, with obvious jank.
- **Fix**: three device tiers (as designed / halved + 30 fps / static image), detected once at start and measured again at runtime, only ever stepping down, never up. See `motion-perf.md`.

## 14. Celebrations without restraint

- **Approach A**: a WebGL energy orb in the middle of the long-task progress animation. Verdict: "The ball in the middle doesn't look good, and the animation doesn't feel premium either."
- **Approach B**: at the moment of completion, a solid colour fill, a central spark burst and a hard-edged white ring. Verdict: "I love the process, but the success moment doesn't feel premium."
- **Fix**: draw the progress onto the brand mark. The mark starts as a faint outline, and a glowing pen "engraves" it stroke by stroke along the contour; how far the pen has gone is how far the progress has gone. On completion, the pen tip turns into light and spreads outward along the engraved lines, with a little light dust and one soft ripple. Only black, white and one accent colour throughout.
- **Principle**: completion should glow, not explode.

## 15. Rebuilding pages that already work

- **Approach**: on three separate occasions, the activation page, the payment page and the store homepage were each torn down and rebuilt from scratch, every time with a new philosophy and a new skeleton, and every time with high engineering quality.
- **Result**: the activation page was rolled back on launch day ("I still prefer the original UI"); the payment page was halted before launch ("It's not as comfortable as the current version"); the new store went live and was then fully reverted, keeping just one detail (a brand wordmark).
- **Why**: for a page users already know and find comfortable, the risk of a full rebuild far outweighs the benefit.
- **Fix**: improve incrementally. Change one section at a time, and ship and verify each change on its own. Big redesigns start as a local preview, with screenshots reviewed before anyone decides.

## 16. Share cards: striking or restrained?

- **Approach**: the first version of the site's social share preview image had a dark cosmic background, multicolour glows and product renders filling the frame. Very striking.
- **Result**: the final choice was the minimal version: an ivory background, one line of a light serif wordmark, one line of widely tracked uppercase subtitle, and nothing else.
- **Principle**: striking is for looking at; for anything that represents the brand over the long term, choose restraint.
