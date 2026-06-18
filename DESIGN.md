# GJA Consulting — Design System & Notes

Reference for future redesigns and improvements. The whole site is a single
self-contained `index.html` (inline `<style>` + vanilla JS, no build step).

## Subject & positioning

- **Who:** Boutique BFSI (Banking, Financial Services & Insurance) consulting,
  based in Singapore. Founded by **Guillermo Arbeiza** — former Chief Customer
  Officer of Lazada and Singlife, ex-Amazon and Singapore Exchange.
- **Audience:** C-suite / heads of digital at banks, insurers, and digital
  businesses in Southeast Asia.
- **Page's single job:** convey senior, operator-level credibility and get a
  conversation started (WhatsApp or the contact form).
- **The strongest asset is the founder's CV** — keep it high on the page
  (hero credibility row), never buried.

## Brand mark

- `assets/img/logo.png` is the GJA mark: a high-contrast serif "GJA" + spaced
  sans caps "CONSULTING" inside an open bracketed square, on a slate-grey field.
- **Gotcha:** the file is actually a **JPEG** (despite the `.png` name) with a
  baked-in opaque slate background sampled at **`#585C5F`**. It has no
  transparency.
- Because the background is opaque slate, the logo only sits cleanly on a
  surface of the *same* slate. That's why slate is a core palette color — on
  ink or bone the logo reads as a grey box. **If a transparent PNG/SVG of the
  mark ever becomes available, swap it in** to remove a faint edge-seam visible
  where the JPEG slate meets the CSS slate (most noticeable on the mobile band).
- The open-bracket square is the brand **signature**; reuse it (e.g. the
  bracket-framed founder portrait) rather than inventing new decoration.

## Palette (CSS custom properties in `:root`)

| Token        | Value                     | Role |
|--------------|---------------------------|------|
| `--ink`      | `#1e2023`                 | Deepest surface: hero base, footer band |
| `--ink-2`    | `#2a2d31`                 | Modal background |
| `--slate`    | `#585c5f`                 | **The logo's grey.** Top bar, hero panel, contact card — wherever the logo lives, so it merges |
| `--slate-d`  | `#4a4e51`                 | Slate borders/shading |
| `--bone`     | `#ede9e4`                 | Light section background |
| `--bone-2`   | `#f5f2ee`                 | Cards, headings on dark |
| `--stone`    | `#6b6f73`                 | Secondary/body text |
| `--brass`    | `#a6843c`                 | Single accent: rules, eyebrows, hovers |
| `--brass-l`  | `#c4a55f`                 | Lighter brass: growth line, emphasis, on-dark accents |
| `--line`     | `rgba(30,32,35,0.14)`     | Hairlines on light |
| `--line-d`   | `rgba(237,233,228,0.18)`  | Hairlines on dark |

Brass is the only accent — spend boldness there and keep everything else quiet.
Avoid the AI-default looks (cream + terracotta serif; near-black + acid green;
broadsheet hairline grid). The slate-from-the-logo + brass direction is the
deliberate, brand-derived choice.

## Typography

- **Display:** Fraunces (Google Fonts), weights 400/500/600. High-contrast serif
  that echoes the logo's "GJA". Hero title uses **400** (500 read too heavy).
- **Body:** Inter, 300/400/500/600. More spine than the original Open Sans.
- **Utility/labels:** Inter in spaced uppercase caps (`.eyebrow`), echoing the
  logo's "CONSULTING" tracking.
- **Long-form body copy is uniformly 16px** (hero sub, cards, approach, bio) —
  a deliberate earlier request; keep it consistent.

## Layout & sections

Top bar (slate) → Hero (ink + slate panel) → How we help (bone, typographic
cards) → About (bone-2: approach + bracket-framed founder portrait) → Contact
(ink + slate logo card) → Footer (near-black).

- Container max-width ~1080–1240px depending on section; 2rem side padding.
- **No stock photography.** Earlier versions used generic glowing-dashboard
  stock; all removed. Service cards are typographic (brass numeral + label +
  one tight sentence). Only real photos kept: the logo and the founder headshot.

## Hero (the centerpiece — most-iterated area)

Structure: `.hero` (ink, `overflow:hidden`) → `.hero-inner` (centered grid:
`minmax(0,1fr) var(--panel-w)`) → `.hero-content` (copy) + `.hero-panel` (slate).

Key decisions and the reasons behind them:

- **Fixed height `min-height: 520px`** on `.hero-inner`. This matches the SVG
  `viewBox="0 0 400 520"` so the growth-line art maps ~1:1 and animation
  positioning is predictable.
  - **Gotcha:** do **not** use viewport-height units (`vh`) in `.hero-content`
    padding — they make the hero grow on taller displays. Padding is fixed
    (`2rem 0`).
- **`--panel-w: clamp(280px, 32vw, 440px)`** keeps the slate logo panel next to
  the title at all widths. An earlier version pinned the panel to the far-right
  viewport edge and it drifted away from the title on wide screens — don't do
  that.
- The slate panel **bleeds to the right viewport edge** via
  `.hero-panel::after { left:100%; width:50vw }`, so it matches the full-width
  top bar instead of floating as a column. `.hero { overflow:hidden }` clips the
  bleed to prevent horizontal scroll.
- **Hero copy order = credibility first:** eyebrow → serif title
  ("Digital, CX, and Data") → tagline ("Growth advice from someone who has *run*
  the business.") → sub → "BUILT AT" row (Lazada · Singlife · Amazon · Singapore
  Exchange). The "built at" row is the single highest-impact element.

### Growth-line animation (the brass curve)

Represents "move the needle" / growth. In `.hero-rise` SVG:

- `viewBox="0 0 400 520"`, **`preserveAspectRatio="xMidYMid meet"`**.
  - **Lesson:** `slice` crops the top of the SVG on short/wide panels and was
    hiding the end marker — `meet` always shows the whole drawing.
- `.rise-path`: smooth single-dip S curve (cubic béziers, not a zigzag/sawtooth
  — that was rejected). Routed mostly to the **right of and behind** the centered
  logo so the logo sits on top of it (brand showcased over the line). Uses
  `pathLength="1"` + `stroke-dasharray:1; stroke-dashoffset:1` so the draw-in
  works without measuring path length. `vector-effect: non-scaling-stroke` keeps
  the stroke crisp at any scale.
- `.rise-grid`: faint horizontal "Y-axis level" lines (ledger feel, subtle BFSI
  nod), drawn behind everything. Extend `x1/x2` beyond the viewBox so they span
  the full panel width.
- **End marker:** a small brass **dot** (`.rise-dot`) sitting exactly on the
  line's terminus `(360,132)`, with a single expanding **halo pulse**
  (`.rise-ping`). History here: arrow → 4-point sparkle → 7-point sparkle → dot
  (the dot is what stuck). The pulse: **one** iteration, starting **at the same
  time the dot pops in** (`animation: ping 2.4s ease-out 1.6s 1`).
- Timing: line draws `1.5s` after `0.3s`; dot pops at `1.6s`; ping fires once at
  `1.6s`. `.hero-content` fades up (`rise-in`) on load.
- The growth line is **hidden on mobile** (`@media max-width:820 { .hero-rise{
  display:none } }`) — at the short, wide band aspect `meet` shrinks it behind
  the centered logo no matter where it's routed, so the band just shows the logo.

## Copy guidelines

- Conversational, plain verbs, sentence case, specific over clever.
- **No em dashes** anywhere user-facing (use commas/colons). This was an explicit
  preference — keep it when adding copy.
- De-jargoned: avoid "leverage", "cross-channel engagement strategies",
  "intersection of", "what sets us apart", etc. Lead with what was actually done
  ("having done digital transformation across Asia's varied markets, not just
  advised on it").
- Section headings end with a period and have a point of view
  ("Three levers to move the needle.", "A boutique by design, not by accident.").

## Accessibility & quality floor

- Modal: focus moves into the first field on open, **focus trap** on Tab, focus
  **returned to the trigger** on close; Esc + overlay-click close it.
- `:focus-visible` brass outlines on all interactive elements.
- `prefers-reduced-motion`: disables animations, freezes the line drawn and the
  dot shown, hides the ping.
- Responsive breakpoints: 820px (stack hero, single-column cards/grids, logo
  band) and 480px (tighter padding, smaller top-bar logo).

## Contacts & integrations (context)

- WhatsApp: `https://wa.me/6594468074` (number not displayed openly per request).
- Contact form: Formspree `https://formspree.io/f/xjgdqbbz`.
- Footer LinkedIn: company page `https://www.linkedin.com/company/gja-consulting/`.
- Hosting: Cloudflare Pages/Workers (config added on the remote;
  `cloudflare/workers-autoconfig`). Repo: `github.com/arbeiza/gja-consulting.com`.

## Workflow notes

- Commits go to `main` (this repo's established pattern). End commit messages
  with the `Co-Authored-By: Claude Opus 4.8` trailer.
- The remote occasionally gets Cloudflare auto-config commits — **rebase onto
  `origin/main`** before pushing if a push is rejected (the autoconfig touches
  separate files, so it rebases cleanly).
- To preview: open `file://…/index.html` directly, or use the Cloudflare/Pages
  dev server. Verify hero at 1440px+ (wide), ~820px, and 390px (mobile).
