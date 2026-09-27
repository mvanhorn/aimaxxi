# Feature 2 — Enlistment card (shareable Maxxi manifesto card)

Net-new: the schism already says **PICK A SIDE** and then gives the visitor nothing to pick. This adds an enlistment step: choose a doctrine pillar, optionally sign a name, and leave with a shareable card that looks like the rest of AI/MAXXI. Homepage and per-pillar links also gain real Open Graph images, which the site currently lacks entirely.

Independently shippable. Does not require Feature 1. Does not edit `#memes`. Does not call any network API at runtime. No secrets.

## User value

Aimaxxi is an identity movement (maxxis vs decels) wearing a token. The analog that actually spread in this culture is a *badge*: people putting `e/acc` in an X name, or posting a belief artifact, not staring at a price. The schism section ends with "every holder is a combatant" but there is no enlistment, no artifact, and no `og:image` — a pasted aimaxxi.com link unfurls without a picture.

The visitor picks **THE MAXXIS** (the only side we offer as a card; decels stay the mute foil), picks one of the six pillars, types a short callsign or leaves `A MAXXI`, and gets a 1200×630 card in Anton / Space Mono / ↗↗ on `#050505`. They download a PNG, native-share it, or post to X with prepared text. Dedicated per-pillar URLs (`/cards/memes.html`, etc.) carry matching static `og:image` files so a *link* unfurl also looks on-brand.

Custom names live on the client canvas (attached as a file). Crawlers cannot run canvas, so OG images are the unnamed template for that pillar — still a complete, beautiful card. That split is the honest static-first design; it does not need a serverless image renderer.

This wins over a live price panel because the page is already "UP ONLY" theater with joke stats, and the chart is one click away. It wins over a multi-question quiz because the site’s own voice is "no meetings about meetings" — one click is the quiz.

## UX flow

Insert a new `<section id="enlist">` immediately after `</section>` of `#schism`, before the war-plan section. Do not rewrite the schism cards; add a one-line cue at the bottom of schism, still in `.schism-foot` voice: `ready to pick? enlist below ↗` linking to `#enlist`.

1. Kicker `ENLIST ↗`, title `PICK A SIDE`, sub `THE DECELS CAN KEEP THE BRAKE. SIGN THE CARD.`
2. **Callsign** field, labeled `CALLSIGN — OPTIONAL`, maxlength 24, default placeholder `A MAXXI`. Empty means the card prints `A MAXXI`.
3. **Pillar grid**: the same six names as the doctrine cells, as a radio group of sharp tiles. Default `06 MAXIMUM MEMES` (the transmission layer — on-brand for a share card). Selecting one updates the live preview immediately.
4. **Live preview**:
   - Landscape card canvas 1200×630 (OG / X in-feed).
   - Toggle `STORY 1080×1350` vs `CARD 1200×630`, default CARD. Story is for mobile share sheets.
   - `role="img"` + computed `aria-label`.
5. Card layout (both sizes, scale type):
   - Full `--bg`.
   - Top Space Mono kicker: `AI/MAXXI ENLISTMENT ↗↗`
   - `icon.png` mark.
   - Anton line: `I AM A MAXXI.`
   - Anton / Archivo: `{CALLSIGN}`
   - Space Mono: `PILLAR 0N — MAXIMUM {PILLAR}`
   - One doctrine sentence for that pillar (copy from the existing `.cell p` text, shortened to one line if needed).
   - Bottom rule: `NO BRAKES. NO CEILINGS. ↗  aimaxxi.com`
   - Optional faint outline word behind: `MAXXI` in stroke type, like `.out`.
6. Actions:
   - `DOWNLOAD PNG ↗` — `.btn.btn-solid`
   - `SHARE ↗` — `.btn.btn-ghost` — Web Share with file when possible
   - `POST TO X ↗` — intent text: `{CALLSIGN} enlisted. pillar: maximum {pillar}. no brakes. no ceilings.` + URL `https://aimaxxi.com/cards/{pillar}.html` (not a hash URL — crawlers need a real path)
   - Helper: `DOWNLOAD THE PNG TO ATTACH YOUR CALLSIGN. THE LINK CARRIES THE PILLAR.`
7. **Decels**: no enlist button on the brake-pedal card. The point of the schism is that you do not pick that side. Do not generate a "I am a decel" meme on this page.
8. Deep link on the homepage: `#enlist?p=energy&n=ALEX` restores pillar + callsign (JS only). Unknown `p` → memes. Sanitize `n` to 24 chars, strip markup (canvas text, not innerHTML).

On the thin card pages (`cards/{pillar}.html`): no editor. Hero is the static PNG, one sentence, buttons `ENLIST YOURS ↗` (to `/#enlist?p=…`) and `BUY ↗`. These pages exist so X/Slack/iMessage unfurl `og:image`.

## Files and sections to touch

| File | Change |
|------|--------|
| `index.html` | (1) Head: `og:title`, `og:description`, `og:url`, `og:image` → `https://aimaxxi.com/og.png`, `twitter:card` = `summary_large_image`, `twitter:image` same. (2) CSS after the schism block (`/* ---------- schism ---------- */`), before war-plan styles. (3) Nav desktop: add `ENLIST` after `SCHISM` (`<a href="#enlist">ENLIST</a>`). (4) Mobile menu: same, plus a sixth `.mmenu.open a:nth-child(6){transition-delay:.40s}`. (5) Schism foot link to `#enlist`. (6) New section `#enlist`. (7) `<script src="enlist.js" defer></script>` **immediately before** the existing inline `<script>` (Feature 1 puts its tag *after* that inline script — different lines). |
| `enlist.js` | **New.** Editor, canvas, share, hash parsing. |
| `og.png` | **New.** 1200×630 default site card (lockup + mark + `MAXIMUM INTELLIGENCE. MAXIMUM MEMES.`). Used by the homepage meta tags. |
| `cards/compute.png` (and intelligence, agents, robots, energy, memes) | **New.** 1200×630 unnamed template for each pillar. Produced once during implementation (see below), committed as static files. |
| `cards/compute.html` (same six slugs) | **New.** ~20-line HTML each: own `og:image` pointing at the sibling PNG (absolute `https://aimaxxi.com/cards/compute.png`), plus a minimal on-brand body. |
| `deploy.py` | Append `"enlist.js", "og.png"` after `"hero-bg.jpg"`, then the twelve `cards/*` paths. Do not reorder the existing six files. |
| `README.md` | Rows for `enlist.js`, `og.png`, and `cards/` at the **end** of the file table (Feature 1 inserts after `memes.jpg`). |

Do **not** add `/api`. A serverless OG renderer would need `@vercel/og` or a PNG encoder — that is a build/npm step. Static PNGs + thin HTML are the static-first equivalent and are enough for unfurls.

Do **not** edit `#memes`, `#buy`, the joke `.stats` row, or `forge.js`.

### How the six (plus one) PNGs get made

There is no production build. During implementation, the same `drawCard()` used at runtime is invoked in a one-off browser pass:

1. Open the local enlist UI.
2. Leave callsign empty (renders `A MAXXI`).
3. For each pillar, DOWNLOAD the 1200×630 canvas and save as `cards/{slug}.png`.
4. One extra variant with no pillar emphasis (or memes pillar + lockup) saved as `og.png` for the homepage.

Check them in. Crawlers never run JS.

Slugs (stable): `compute` `intelligence` `agents` `robots` `energy` `memes`.

## Approach (static-first)

1. **Canvas renderer** in `enlist.js`, `drawCard(ctx, {w,h,name,pillar})`. Load `icon.png` (and optionally `lockup.png`, currently unused on the page — allowed here). Wait on `document.fonts.ready` / `fonts.load('72px Anton')`.
2. **No runtime APIs.** Share and download are local. X intent is a query-string URL.
3. **Hash state** on `index.html` only. Card HTML files are static and dumb.
4. **Sanitize** callsign: strip control chars, no emoji required (Anton will not render them well); map unknown to `A MAXXI`.
5. **Absolute OG URLs** in meta tags (`https://aimaxxi.com/...`) because relative `og:image` is unreliable on X.
6. **Vercel**: extra files in the repo root / `cards/` deploy as static. Git integration already publishes whatever is in the tree. Update `deploy.py` so a manual API deploy does not drop them.
7. **Why not `/api/og`:** dynamic callsigns in the unfurl would be nice and would justify a function *if* we had a no-dep PNG encoder. We do not, and adding `package.json` violates the no-build rule. File attachments carry the callsign; the link carries the pillar. That is acceptable and still meaty.

## Design system fit

- Same tokens, same three fonts, same 0 radius, same kickers and section titles.
- Pillar tiles copy `.grid` / `.cell` (1px `--line` as grid gap, `--bg` cells, Space Mono numbers `01`–`06`, Anton `h3`).
- Card canvas should look like a screenshot of the schism *maxxi* panel, not a rounded "Twitter card" template: no gradients other than the existing faint ink wash `rgba(244,244,240,.03)`, no color besides ink/black (do not steal CRT green from the forge).
- Outline display type for the ghost `MAXXI` word, 1.5px stroke (1px on small story canvas).
- Buttons: `.btn.btn-solid` / `.btn-ghost`.
- Nav link is not `.hot` (BUY stays the only hot link).
- Voice: `ENLIST`, `CALLSIGN`, `PILLAR`, `↗`. No "sign the petition," no "airdrop," no APY.
- Keep the global NFA footer; do not put financial language on the card.

## Mobile, accessibility, performance

**Mobile**

- Pillar grid: 2 columns at ≤900px, 1 column at ≤640px (same breakpoints as `.grid`).
- Preview `width: 100%`; story/card toggle above it.
- SHARE is the primary CTA on coarse pointer; DOWNLOAD still visible.
- New nav item must wrap; desktop `nav .links a` uses `margin-left: 28px` and can overflow. At 900px the burger already hides links — no extra breakpoint. Between 900 and ~1100px, seven items may crowd: drop letter-spacing on nav links to `.18em` only inside a new `min-width: 900px and max-width: 1100px` query, or allow wrap. Do not hide ENLIST.
- `scroll-margin-top` already on `section`.

**Accessibility**

- `fieldset` / `legend` for pillars (radio inputs visually custom but native).
- Visible label on callsign.
- Canvas `role="img"` + `aria-live="polite"` on a sibling status: `Enlisted as {name}, pillar {pillar}.`
- Focus ring on new controls: `2px solid var(--ink)`.
- Card pages: real `<h1>`, image `alt` = `AI/MAXXI enlistment card, maximum {pillar}`.
- Reduced motion: no extra animation on tiles (the page hover `translateY` already disables under the existing reduce-motion rule for `.rv`; add the same for any new pulse).
- Do not auto-play share. Do not steal burger's `aria-expanded` logic; just add a link that closes the menu because it is `href="#enlist"` and the existing handler already closes on `a[href^="#"]`.

**Performance budget**

- `enlist.js` ≤ 16KB uncompressed.
- Homepage does **not** download the six `cards/*.png`. Only `og.png` is referenced from homepage meta (crawlers fetch it, browsers usually do not).
- `og.png` target ≤ 120KB. Each pillar PNG ≤ 80KB (flat graphics compress well).
- Decode `icon.png` once. Single canvas, swap dimensions on CARD/STORY toggle.
- `enlist.js` is `defer`; work starts on DOMContentLoaded. Optional: delay `fonts.load` until `#enlist` intersects so the rest of the page is unaffected.

## Test / verification plan

Serve from repo root over HTTP (`python3 -m http.server 8080`).

**Functional**

1. Schism unchanged; foot link scrolls to `#enlist`.
2. Default preview: `A MAXXI` / MAXIMUM MEMES / 1200×630.
3. Each of six pillars redraws; download six PNGs and spot-check type is not clipped.
4. Callsign `ALEX` and a 24-char string; overflow shrinks, does not leak off the canvas.
5. STORY toggle exports 1080×1350.
6. COPY/SHARE/DOWNLOAD do not throw on desktop Chrome; Safari download works.
7. POST TO X uses `https://aimaxxi.com/cards/memes.html` (or the selected slug), not a hash URL.
8. `#enlist?p=energy&n=GEOFF` on load selects energy and GEOFF.
9. `#enlist?p=bogus&n=<script>` → memes pillar, callsign stripped to safe text, no HTML injection (canvas `fillText` only).
10. Homepage view-source contains `og:image` and `twitter:card`.
11. `cards/compute.html` view-source: its own `og:image` ends in `/cards/compute.png`. Open the PNG directly (200).
12. Burger: ENLIST appears, opens, closes, scrolls.
13. Joke stats, contract copy, collage, buy buttons unchanged.
14. With `enlist.js` blocked: section copy still readable; canvases empty; card HTML pages still unfurl because they do not need JS.

**Crawler stand-in**

15. `curl -s localhost:8080/cards/energy.html | grep og:image`
16. If a public preview deploy exists, paste a `cards/memes.html` URL into https://cards-dev.x.com/validator (or Slack unfurl). If the environment cannot reach X's validator, screenshot the static PNG next to the HTML and note that unfurl was not live-checked.

**Responsive / a11y**

17. 390×844: grid, preview, stacked buttons, no horizontal page scroll.
18. 1280×800: enlist beside itself (preview left, controls right is allowed if it matches `.sides` two-column). Prefer **preview above controls on mobile, two-column from 900px** to echo `.sides`.
19. Keyboard through radios and actions.
20. Reduced motion: no new animation.

**Screenshots to take** (with the implementation PR, not this planning commit):

- `enlist-desktop.png` — full section, default memes pillar.
- `enlist-desktop-named.png` — callsign filled, compute pillar, two-column.
- `enlist-mobile-390.png` — stacked.
- `enlist-story.png` — story size preview.
- `enlist-export-card.png` — the 1200×630 PNG file.
- `enlist-card-html.png` — `cards/memes.html` as a visitor sees it.
- `enlist-nav-mobile.png` — burger with ENLIST.
- `enlist-og-home.png` — optional, if an OG debugger is available.

## Risks

- **Wording vs NFA.** "Enlist" / "combatant" is already the site's metaphor. Do not add "pledge 1% of supply" or holder verification — that slides toward commitment language a lawyer would hate. Keep the footer.
- **Custom name not in OG.** Documented in the helper line. If the owner later wants named unfurls, that is a *new* PR with a justified `/api` renderer and a dependency discussion — not this one.
- **Nav crowding** on mid-width desktops. Spec above; verify at 1024px.
- **Six HTML files** feel noisy in a repo that is otherwise one page. They are the cost of static OG. Do not collapse them into query params (crawlers will not execute JS).
- **Font swap** on canvas before `fonts.ready` can export Arial. Disable DOWNLOAD until fonts are ready, or redraw and replace; show `LOADING TYPE ↗` on the button.
- **`lockup.png`** is unused today; using it on the card is a plus. If it looks soft at 1200px, prefer drawing `AI/MAXXI` in Anton (the lockup is a raster).
- **Merge conflicts with Feature 1:** avoided if CSS stays in the schism block, HTML stays after `#schism`, script src stays *before* the inline script, and README/deploy edits are at the end of those lists.
- **Production deploy list:** same `deploy.py` trap as Feature 1 — append the new files.

## Out of scope

- Meme generator, live DexScreener tape, wallet-gated "verified holder" seals, serverless PNG, quiz questions, decel-side cards, new colors, new fonts.

## Rough size

~90 lines HTML + ~100 lines CSS in `index.html`, ~300 lines `enlist.js`, 6× ~20-line card HTML files, 7 PNGs generated from the same renderer, plus README/`deploy.py` appends. Logic is small; the meat is the card craft and the static unfurl paths.
