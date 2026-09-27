# Feature 1 — Transmission Forge (in-browser meme generator)

Net-new: visitors can mint on-brand AI/MAXXI memes in the browser, then download or share them. The page currently *talks* about memes as the transmission layer and then shows a single static collage. This makes `#memes` a tool, not a poster.

Independently shippable. Does not require Feature 2. Does not add nav items (reuses `#memes`). Does not add Open Graph tags (file-based share). Does not call any network API at runtime.

## User value

The live page is a manifesto with a buy button. The collage in `#memes` is strong but read-only; on mobile it stays grayscale and there is no way to leave with an artifact. People who would post for the movement have to screenshot the site or leave for a generic meme app that does not have Anton, the ↗↗ mark, or the doctrine lines.

A client-side forge turns lurkers into transmitters. The output is a 1080×1080 PNG that looks like it came from the same shop as the collage: black field, sharp corners, giant type, ↗↗, optional `hero-bg.jpg` wash, optional user photo with the mark stamped on it. Caption presets are the lines the page already uses ("10x is cowardice…", "if it can be memed, meme it.", "no brakes. no ceilings."). Sharing is the actual PNG (the way memes spread), plus a copyable caption and an X intent that pastes the caption and `https://aimaxxi.com/#memes`.

This is the feature that matches the site's own war-plan line "mint the memes" without turning aimaxxi.com into a generic token dashboard or a Buzzfeed quiz.

## UX flow

All of this lives under the existing `#memes` section, below the collage, above the caption line (which stays, retargeted at the forge on desktop).

1. Visitor reaches **THE MEMES**. Collage is unchanged (`memes.jpg`, lazy, grayscale → color on desktop hover).
2. New block: kicker `MINT THE MEMES ↗`, title `THE FORGE`, sub `ONE CAPTION. ONE MARK. TRANSMIT.`
3. **Template row** (horizontal scroll on mobile, wrap on desktop). Sharp-edged thumbs, no radius. Eight templates, all drawn in canvas from brand primitives — not slices of the collage:
   - `FLAG` — black, giant ↗↗ from `icon.png`, caption stacked in Anton.
   - `OUTLINE` — transparent fill + ink stroke, matching `.out` headings.
   - `TAPE` — Space Mono kicker on top, Anton payload, bottom rule.
   - `CRT` — black, green ink (`#7dff9a` only here, as a CRT phosphor, still sharp corners), scanlines; respects `prefers-reduced-motion`.
   - `HERO` — `hero-bg.jpg` full-bleed, existing hero gradient wash, ink type on top.
   - `LIST` — four checked maxims (compute / intelligence / agents / memes) + caption.
   - `PILLAR` — one of the six doctrine names as the hero word; caption is the pillar body line.
   - `STAMP` — visitor image, forced through the site filter (grayscale + contrast), mark + `AI/MAXXI` lockup in the corner.
4. **Caption**: `<select>` of 10–12 doctrine/marquee lines, plus `CUSTOM` revealing a text input, maxlength 96, `autocomplete="off"`. Placeholder `10x is cowardice.`
5. **Pillar** (for `PILLAR` template, hidden otherwise): six buttons styled like `.cell` titles, default `MAXIMUM MEMES`.
6. Live **preview**: a square canvas, `width/height` attributes 1080, CSS `width: 100%; max-width: 540px`. `role="img"` and a computed `aria-label` ("AI/MAXXI meme, FLAG template, caption …").
7. Actions, using existing button classes:
   - `DOWNLOAD PNG ↗` — `.btn.btn-solid` — `canvas.toBlob` → `<a download="aimaxxi-{template}.png">`.
   - `SHARE ↗` — `.btn.btn-ghost` — `navigator.share` with the file when supported; if the browser cannot share a file, fall back to download + copied caption. Never fail silently.
   - `COPY CAPTION` — same behavior as the existing contract copy button (Clipboard API, textarea fallback, label `COPIED ↗` for 2s).
   - `POST TO X ↗` — `https://twitter.com/intent/tweet?text=` with caption + `aimaxxi.com` + the ↗↗. Image is not attachable via intent; the downloaded PNG is what they attach. Helper line under the row, Space Mono 11px muted: `DOWNLOAD THE PNG. ATTACH IT. TRANSMIT.`
8. Empty/error states:
   - Fonts still loading: preview renders in Impact / Arial, then redraws on `document.fonts.ready`.
   - `STAMP` with no photo: dashed ink frame, `DROP A FACE. STAMP THE MARK.` and a file button. Canvas stays untainted.
   - Share unsupported: `SHARE` button becomes `DOWNLOAD + COPY` and performs both.
9. No wallet, no account, nothing stored except what they download. Do not write localStorage.

Deep link: `#memes` is enough. Optional query `#memes?t=flag&c=0` can preselect template + caption index; ignore unknown values.

## Files and sections to touch

| File | Change |
|------|--------|
| `index.html` | CSS after the existing `/* ---------- memes ---------- */` block (~line 170). HTML inside `<section class="memes" id="memes">` after the collage `<img>`, before `.cap`. One new tag: `<script src="forge.js" defer></script>` **immediately after** the existing inline `</script>`, before `</body>`. Do not edit nav, schism, stats, or the inline burger/counter/copy script. |
| `forge.js` | **New.** All forge behavior. No framework. |
| `deploy.py` | Add `"forge.js"` to `FILES` immediately after `"memes.jpg"`. |
| `README.md` | Add a `forge.js` row to the file table, after `memes.jpg`. |

Do **not** add images. Do **not** add fonts. Do **not** add `/api`. Do **not** modify `memes.jpg`, `icon.png`, `hero-bg.jpg`.

`index.html` insertion points (so a later enlistment PR can merge cleanly):

- CSS: only under the memes comment, before `/* ---------- final ---------- */`.
- HTML: only inside `#memes`.
- Script src: after the inline script, not before it.

## Approach (static-first)

No build, no npm, no secrets, no runtime HTTP.

1. **Draw in canvas.** Templates are functions `drawFlag(ctx, w, h, state)`, etc. They load `icon.png` and (for HERO) `hero-bg.jpg` via `Image()` from same-origin URLs. Note: `hero-bg.jpg` is a WebP bitstream with a `.jpg` name; `Image()` still decodes it. Do not rename in this PR.
2. **Fonts.** Use the three already on the page. Before the first paint of Anton/Space Mono on canvas: `document.fonts.load('80px Anton')` (and Archivo, Space Mono), then `document.fonts.ready`. If a load fails, keep Impact / `'Courier New'` fallbacks — still readable, still sharp.
3. **Lazy load.** `forge.js` is `defer` and small. Inside it, do **not** decode `hero-bg.jpg` until the HERO template is selected. Do not decode a user photo until `STAMP` is selected. Kick off `icon.png` load on first intersection of `#forge` (a wrapper id inside `#memes`) via IntersectionObserver, matching the existing `.rv` pattern.
4. **Export.** `canvas.toBlob('image/png')`. Do not use `toDataURL` for download (memory). Revoke object URLs after click.
5. **STAMP / taint.** User files come from `<input type="file" accept="image/*">` or drop. Draw via `createImageBitmap` / `Image`. Never send the photo anywhere. If `toBlob` fails because of taint (should not, same-origin + local files), disable SHARE/DOWNLOAD and show `COULD NOT EXPORT — TRY ANOTHER IMAGE`.
6. **Do not use `memes.jpg` as a sprite sheet.** The collage includes recognizable likenesses. The forge's job is to mint *new* munitions in the same *system*, not to crop Elon or Goku.
7. **Caption list** (source of truth: copy already on the page):
   - `10x is cowardice. we want 1,000,000x.`
   - `if it can be automated, automate it.`
   - `if it can be scaled, scale it.`
   - `if it can be memed, meme it.`
   - `no brakes. no ceilings.`
   - `no vibes left unoptimized.`
   - `intelligence is the ultimate resource.`
   - `memes are the transmission layer.`
   - `the decels are organized. so are we.`
   - `stop optimizing. start maxxing.`
   - `maximum compute. maximum memes.`
   - `CUSTOM`

Redraw on input (rAF-coalesce). Keep a single 1080 canvas; CSS scales it. On mobile, still export 1080 so the PNG is useful on X.

## Design system fit

Reuse, do not invent:

- Color tokens: `--bg`, `--ink`, `--mut`, `--faint`, `--line`. CRT green is the only new color and only on that template.
- Type: Anton for payload, Space Mono for kickers/labels, Archivo for helper text.
- Controls: existing `.btn`, `.btn-solid`, `.btn-ghost`, same padding / `min-height` / letter-spacing. Selects and inputs: 0 radius (the global `* { border-radius: 0 !important }` already forces this), 1px `--line` border, `--bg` fill, `--ink` text, 48px min-height, 16px font on inputs to avoid iOS zoom.
- Template chips: look like `.step` / `.cell` — 1px line, pad, hover `translateY(-2px)` only when `@media (hover:hover)`.
- Section header: `.sec-kick` / `.sec-title` / `.sec-sub`.
- Reveal: add `.rv` on the forge wrapper so it fades in with the rest of the page.
- Images in-page stay grayscale; the **exported PNG** may be full ink-on-black or HERO photographic — the export is the munition, not a page image, so do not wrap the canvas in the global `img { filter: grayscale }` (canvas is not `img`, so it is already safe).
- Copy voice: lowercase payload, `↗` on actions, no emoji, no "🚀 moon" lexicon.

## Mobile, accessibility, performance

**Mobile**

- Template row: `display: flex; overflow-x: auto; gap: 10px; -webkit-overflow-scrolling: touch;` thumbs `min-width: 96px`.
- Stack preview then controls (`flex-direction: column`).
- Full-width buttons, same as `.cta-row` at 640px.
- `STAMP` uses `capture` optional; do not require camera.
- Safe-area: inherit page padding. Preview `max-width: 100%`.
- Web Share is the primary mobile path; download still present (iOS Safari often prefers share).

**Accessibility**

- Forge wrapper is a labeled region: `aria-labelledby` pointing at the visible title.
- Template chips are `role="listbox"` / `role="option"` or a radio group (`fieldset` + `legend` "Template") so keyboard works. `aria-selected` on the active chip.
- Caption select and custom input have visible `<label>` in Space Mono, not placeholder-only.
- Canvas has `role="img"` and a live `aria-label`. Adjacent visually-hidden text mirroring the caption for AT that skip canvas.
- `:focus-visible { outline: 2px solid var(--ink); outline-offset: 3px; }` on new controls only.
- `prefers-reduced-motion: reduce`: no CRT scanline animation, no hover translate, no extra rAF loops beyond a single redraw.
- Do not `overflow: hidden` on body. Do not trap focus.
- Color is not the only state: selected chip gets `border-color: var(--ink)` and a Space Mono `SELECTED` tick, not just brightness.

**Performance budget**

- `forge.js` target ≤ 18KB uncompressed. No libraries.
- No new webfonts, no new bitmaps.
- `hero-bg.jpg` (~665KB) is already on the page; HERO template reuses the decoded image if the hero already loaded it, otherwise loads on demand.
- `icon.png` is 13KB and already in nav.
- Do not pre-render all eight templates to bitmaps.
- Coalesce redraws (`needsDraw` flag + one rAF).
- `loading` of `forge.js`: `defer` is enough; optional intersection-based `import()` is unnecessary without a bundler — keep one file.

## Test / verification plan

Serve locally with `python3 -m http.server 8080` from the repo root (or equivalent). Do not rely on `file://` (fonts and images can taint or fail).

**Functional**

1. Desktop 1280×800: collage still grayscale until hover; forge below; each of 8 templates produces a distinct PNG.
2. Change caption presets; CUSTOM at 96 chars truncates, no layout break.
3. PILLAR template: all six pillars update the hero word.
4. DOWNLOAD produces a ~1080×1080 PNG; open it — fonts look like Anton, mark is sharp, no page UI chrome.
5. COPY CAPTION matches the canvas caption; button label resets after 2s (same as CA copy).
6. POST TO X opens an intent with caption + aimaxxi.com (popup blocker: user gesture required — click, not on load).
7. STAMP: upload a photo, confirm grayscale+mark; export works; original photo is not uploaded anywhere (Network panel: no extra requests).
8. HERO: `hero-bg.jpg` appears under type; if the image is slow, preview still shows type on black then redraws.
9. With `forge.js` blocked (devtools): collage and the rest of the site still work. Forge UI may show a `ENABLE JAVASCRIPT TO MINT` noscript line.
10. Existing contract copy, burger menu, counters, and buy links unchanged.

**Responsive / a11y**

11. 390×844 (iPhone) and 768×1024: template row scrolls, buttons stack, preview not clipped by nav (`scroll-margin-top` already 84px on `section`).
12. Keyboard only: tab to templates, arrows or tab between chips, enter to select, tab to caption and actions.
13. `prefers-reduced-motion`: CRT is static.
14. VoiceOver/TalkBack: region name, selected template, canvas label.

**Screenshots to take** (store with the PR, not in this planning commit):

- `forge-desktop-flag.png` — FLAG template, default caption, full section including collage above.
- `forge-desktop-hero.png` — HERO template.
- `forge-desktop-stamp.png` — STAMP with a test photo.
- `forge-mobile-390.png` — full forge stacked, template row mid-scroll.
- `forge-export.png` — the downloaded PNG itself (not the page).
- `forge-reduced-motion.png` — CRT template with reduced motion on.

No API to mock. If Google Fonts is blocked, still screenshot the Impact fallback so reviewers see it is acceptable.

## Risks

- **Likeness / collage:** temptation to crop `memes.jpg`. Out of scope. If someone asks, the answer is no — mint new frames.
- **Font metrics:** Anton on canvas can overflow a 96-char custom caption. Wrap to max 3 lines, then shrink font. Test with `WWWWWWWWWW…`.
- **iOS Web Share + files:** flaky on older iOS. Fallback is download + copy, already specified.
- **Memory:** 1080 canvas + user photo on low-end Androids. Revoke bitmaps; do not keep a second 4K canvas.
- **`deploy.py` path:** the helper still points at `~/workspace/aimaxxi-site` and an explicit file list. Missing `forge.js` in `FILES` means a manual API deploy would ship HTML that 404s the script. Git-based Vercel deploy of the repo root is the real path; still update `FILES`.
- **Merge conflicts:** none with Feature 2 if insertion points above are honored. Both PRs touch `README.md` and `deploy.py` on adjacent lines — keep those edits one-line each.
- **Legal / NFA:** exported memes should not add financial claims. Keep the caption list to existing manifesto lines. Footer NFA stays.

## Out of scope

- Server-side image gen, OG tags, `#enlist`, live token stats, wallet, AI captioning, extra webfonts, slicing the collage, Telegram bots, storing memes.

## Rough size

~80 lines HTML + ~120 lines CSS in `index.html`, ~350–450 lines `forge.js`, plus one-line edits to `README.md` and `deploy.py`. Three files touched, one file added.
