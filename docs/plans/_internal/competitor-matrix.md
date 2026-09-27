# Competitor / analog matrix (internal)

Selection evidence for two net-new aimaxxi.com features. Not for PR copy.

Researched: 2026-09-27. Live site https://aimaxxi.com matches this repo's `index.html`. No Granola or Slack discussion of AI/MAXXI, Geoffrey Woo, or this landing page was found, so selection is from the page itself, the original-post framing in the README, comparable movement/meme/token sites, and what the current static architecture can ship without a build or API keys.

## Aimaxxi baseline (what already exists)

Single static `index.html` + images. No build. No `og:*` / `twitter:card` tags. Design system: `#050505` / `#f4f4f0`, Anton / Archivo / Space Mono, `border-radius: 0`, grayscale images that colorize on desktop hover, `.btn-solid` / `.btn-ghost`, kickers in Space Mono, outline (`-webkit-text-stroke`) display type, IntersectionObserver reveals, joke animated stats (`1,000,000x` / `0 brakes` / `∞ memes`), contract copy, Jupiter + DexScreener outbound links, six-pillar doctrine, decel vs maxxi schism ("PICK A SIDE" with no action), static `memes.jpg` collage, NFA footer.

On-chain (DexScreener public API, CORS `*`, no key): Solana mint `5cQReyzgJQbtbGLvkm1vN9GDGFBWzcAC6TDprBwzVVjL` (same CA the page shows, mixed case). Primary PumpSwap pair ~$75k mcap, ~$1.5M 24h volume, ~$26k liquidity, ~−41% 24h at time of check. DexScreener lists website as usepaid.app, not aimaxxi.com.

## Sites reviewed

| ID | Site | What it is | Features that drive sharing / dwell | Aimaxxi already has? |
|----|------|------------|--------------------------------------|----------------------|
| S1 | [aimaxxi.com](https://aimaxxi.com) | AI-maximalist manifesto + Solana meme token | Doctrine, schism, war plan, static collage, CA copy, buy/chart exits | — |
| S2 | [dogecoin.com](https://dogecoin.com) | Movement currency, not a pump page | Manifesto/ethos, how-to, community links. Explicitly tells people to use *external* meme makers rather than shipping one. | Manifesto + how-to: yes. Community org: no. Generator: no. |
| S3 | pepe.vip / pepecoin.webflow.io | Classic meme-coin lander | Lore, how-to-buy, CA, tokenomics, merch/roadmap promises. Sharing happens off-site (memes of Pepe, not a site tool). | Lore + how-to + CA: yes. Tokenomics/roadmap: no (and would feel off-brand vs "no meetings"). |
| S4 | [bonkcoin.com](https://www.bonkcoin.com) | Mature Solana meme with a real product surface | Holder counts, integrations, games (BonkPlay), collectibles. Identity is "play," not "believe." | Holder/live stats: no. Games: no. |
| S5 | [dogwifhat.us](https://dogwifhat.us) | Official-ish $WIF lander | **Live mcap / supply**, CA, **random meme from a 500+ blob**. Separate community tools: [PFP generator](https://wifpfp.vercel.app/), [dogwifhatgang.com PFP overlay](https://dogwifhatgang.com/). The hat-on-your-face loop is how WIF spread in game lobbies. | Live mcap: no. Random/generated memes: no. PFP overlay: no. Static collage only. |
| S6 | [spx6900.com](https://www.spx6900.com) + [6900isbiggerthan500.com](https://6900isbiggerthan500.com/leaflet/) | Belief-asset *movement* (closest narrative analog) | Terminal manifesto, "stop trading / believe in something," leaflet/book, lore sites. Sharing is **identity + thesis**, not a price widget. No in-page generator. | Manifesto energy: yes. Identity artifact to post: no. |
| S7 | e/acc ([eacc.community](https://eacc.community), wiki, bios) | AI-accelerationist movement aimaxxi is riffing on | Spread by **putting `e/acc` in an X bio / display name**, plus manifesto essays (Andreessen Techno-Optimist Manifesto). Same enemy language ("decels"). Almost no site-native share tool; the *badge* is the feature. | Same schism language: yes. A mintable badge/card: no. |
| S8 | [goatseusmaximus.fun](https://www.goatseusmaximus.fun) | AI-agent meme coin (Truth Terminal) | Lore, ATH brag, CA copy, "agent" framing. Chart/ATH as social proof. | Lore + CA: yes. Live ATH/mcap: no. |
| S9 | MemeOS (open-source meme launch stack) | Passport-card pattern | Server-rendered **Meme Passport PNG** + OG on public "empire" pages. The downloadable identity card is the share primitive. | Identity card: no. OG image: no. |
| S10 | Coine / PumpAI / forge.meme | Generic memecoin website builders | Default blocks: tokenomics, roadmap, embedded chart, swap, AI-written copy, sometimes an AI meme factory. This is the "looks like every other pump.fun coin" kit. | Aimaxxi already *avoided* this kit (joke stats, doctrine, no tokenomics). Copying it would make the page less distinctive. |
| S11 | usepaid.app token page for this mint | Other web surface for $AI/MAXXI | Live mcap, payments tape, chart. Traders who want numbers already have this + DexScreener. | Aimaxxi is the *movement* page, not the tape. |

## Feature candidates (ranked)

Scoring 1–5. **Risk 5 = safest / most mergeable technically.** Composite = user value + novelty + mergeability + risk. Novelty is "not already on aimaxxi," not "never seen on the internet."

| Row | Candidate | User value | Novelty vs aimaxxi | Mergeability (owner + architecture) | Risk (high=safer) | Composite | Notes |
|-----|-----------|------------|--------------------|--------------------------------------|-------------------|-----------|-------|
| **R1** | **In-browser Transmission Forge (meme generator from brand assets)** | 5 | 5 | 5 | 5 | **20** | Site's own thesis is "memes are the transmission layer" / "mint the memes," but `#memes` is a dead JPG. WIF/Doge prove generators and meme kits are how culture leaves the site. Canvas + existing fonts/mark/`hero-bg.jpg`. No backend. |
| **R2** | **Enlistment / manifesto card (pick a side + pillar, download + OG)** | 5 | 5 | 5 | 4 | **19** | Schism already says "PICK A SIDE" with no action. e/acc spread via badges; SPX via belief identity; MemeOS via passport PNGs. Site has **zero** `og:image`, so links unfurl as a blank URL. Static-first OG via committed template PNGs + thin HTML; custom-name cards via canvas. Tiny risk: pledge-adjacent wording vs NFA footer. |
| R3 | Live on-chain stats / "the tape" (DexScreener, keyless, CORS `*`) | 4 | 4 | 2 | 3 | 13 | DexScreener `GET /latest/dex/tokens/{mint}` works from the browser, 30s cache, no key. dogwifhat.us and BONK lead with mcap. **Merge risk:** joke stats are load-bearing comedy; marquee says "UP ONLY" while the pair was ~−41% 24h; three pools disagree; chart is already one click away; usepaid.app already shows mcap. Feels like a Coine/PumpAI template block. |
| R4 | Standalone "which pillar are you" quiz | 3 | 4 | 4 | 4 | 15 | Shareable, but Buzzfeed energy vs "no meetings about meetings." Better as a one-click pillar pick inside R2 than as a multi-question quiz. |
| R5 | PFP ↗↗ overlay only | 4 | 4 | 4 | 3 | 15 | WIF's actual viral loop. User-upload + canvas is doable, but a thin feature alone. Fold a "stamp the mark" mode into R1 rather than a second PR. Upload/privacy and extra bytes are the risk. |
| R6 | Copypasta / tweet-arsenal (copy doctrine lines) | 2 | 3 | 5 | 5 | 15 | Easy merge, not meaty. The CA button already teaches "copy." A generator that *emits* share text is strictly more. |
| R7 | Jupiter swap iframe / plugin embed | 3 | 3 | 3 | 2 | 11 | Buy is already the loudest CTA. Third-party iframe weight, CSP, and mobile wallets. Not shareable. |
| R8 | Holder leaderboard / wallet connect badge | 3 | 4 | 1 | 1 | 9 | Needs RPC/indexer keys or a heavy wallet adapter. Breaks no-build and the performance budget. |
| R9 | Wallpaper / meme-kit ZIP of existing collage | 2 | 2 | 4 | 4 | 12 | `memes.jpg` already is the kit. Downloading it is not a product. Also contains celebrity-likeness frames we should not invite people to remix. |
| R10 | Sitewide OG meta tags only | 2 | 3 | 5 | 5 | 15 | Necessary hygiene, not a feature. Bundle into R2. |
| R11 | Soundboard / "compute go brrr" | 2 | 4 | 2 | 2 | 10 | Autoplay hostility, a11y, not Geoffrey's tone. |
| R12 | Live X / Telegram embed | 2 | 3 | 2 | 1 | 8 | Keys, CORS, layout. The original post is already linked. |
| R13 | Clicker / idle "maxx the FLOPs" game | 3 | 5 | 2 | 2 | 12 | Memorable but fights the brutalist manifesto and invites toy-site comparisons. |
| R14 | Tokenomics + roadmap blocks | 1 | 1 | 2 | 3 | 7 | Template-default (S10). The war plan already covers "what we build" without fake phases. |
| R15 | Fake "army size" localStorage counter | 2 | 3 | 3 | 2 | 10 | Fake stats on a fake-stats page is one joke too many; also misleading. |

## Pick

**Ship R1 and R2.** They are the only two that are simultaneously meaty, native to *this* page's copy, independently shippable in non-overlapping `index.html` regions, and aligned with how analogous *movements* (e/acc, SPX, WIF PFPs, MemeOS passports) actually spread.

- **Feature 1 = R1** — Transmission Forge, extending `#memes`. Plan: `docs/plans/feature-1-transmission-forge.md`
- **Feature 2 = R2** — Enlistment cards, new `#enlist` after `#schism`. Plan: `docs/plans/feature-2-enlistment-card.md`

R3 is the strongest *rejected* candidate: technically easy (DexScreener CORS `*`, GeckoTerminal also CORS `*` as fallback) but narratively wrong for an "UP ONLY" manifesto on a day-old, high-vol, down-day mint, and redundant with DexScreener / Jupiter / usepaid.app. Reconsider only if the owner asks for a tape *in addition to* keeping the joke stats.

## Architecture constraints that killed otherwise-good ideas

- No build step, no `package.json`, no secrets in client code.
- Vercel Git → production on `main`. Extra files must be added to `deploy.py`'s `FILES` list if that helper is still used.
- Free, keyless APIs only, with visible fallback. R1/R2 need **no runtime API**. R3 would have used DexScreener.
- Do not slice `memes.jpg` into a generator (Elon / Einstein / Goku / David likenesses). Use type, `icon.png`, `lockup.png`, `hero-bg.jpg`, and canvas geometry.
- Keep extra weight small; `hero-bg.jpg` (~665KB WebP) and `memes.jpg` (~428KB) already dominate the page.
