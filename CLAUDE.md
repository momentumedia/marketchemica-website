# CLAUDE.md — Market Chemica Associates Website

Operating instructions for any coding agent working in this repo. Read top to bottom before making changes.

---

## TL;DR — top-priority rules

1. **No framework. No build step. No package manager.** This is hand-written static HTML + CSS + JS. Do not introduce React, Next.js, Vite, npm/yarn/pnpm, TypeScript, or a bundler unless explicitly asked.
2. **Header, footer, and `<head>` are duplicated across all 7 HTML files.** Any change to nav, footer, meta, or stylesheet links must be applied to **all seven** pages or the site goes inconsistent. There is no template engine.
3. **Never use `backdrop-filter`, `filter`, or `transform` on an ancestor of a `position: fixed` element.** It creates a containing block and breaks the mobile nav drawer (and would break any future fixed modal). This was a real bug — see *Known pitfalls*.
4. **Use design tokens.** Do not hardcode colors, fonts, font-sizes, or spacing. Reach for `var(--*)` from `assets/css/tokens.css`. Add a new token before introducing a new value.
5. **Banned typography.** Do not use Inter, Roboto, Arial, system-ui, or any "AI-default" sans. Display = `Fraunces`. Body = `Hanken Grotesk`. Mono = `JetBrains Mono`. Loaded via Google Fonts CDN in each page's `<head>`.
6. **Push to `main` deploys to production** via GitHub Pages. There is no staging. Open the live URL after each push and verify before leaving the change.
7. **Reference materials in the repo root (`*.pdf`, `*.zip`, the original JPGs, `SKILL.md`) are gitignored content sources.** They are the source of truth for company copy, deal data, and bios. Do not delete them locally; do not commit them.

---

## Project overview

- **What:** Marketing website for **Market Chemica Associates (MCHA)** — a specialized M&A advisory + management consulting firm focused on fine and specialty chemicals. Established 2012.
- **Primary users:** Founder-owned chemicals SMEs, private equity sponsors, family offices, HNW/UHNW investors, and generalist banks looking for chemistry-fluent diligence/advisory.
- **Business goal:** Replace the existing minimal `marketchemica.com` with a credible, multi-page brochure site that communicates the firm's depth (30+ executive associates, $10M–$10B mandates, 7 knowledge clusters, global reach) and converts qualified leads into partner-level conversations via email/phone.
- **Live (this build):** https://momentumedia.github.io/marketchemica-website/
- **Live (current production domain):** https://www.marketchemica.com/ — TODO: confirm whether/when this build replaces the live domain.

---

## Tech stack

- **Languages:** HTML5, CSS3 (with custom properties + clamp + clip-path), vanilla ES6+ JavaScript.
- **Frameworks/build:** none.
- **Package manager:** none.
- **Fonts:** Google Fonts CDN — Fraunces (variable, with `opsz` + `SOFT` axes + italics), Hanken Grotesk (variable weight 300–700), JetBrains Mono.
- **Hosting:** GitHub Pages (legacy build, `main` branch, `/` root).
- **Local dev server:** `python3 -m http.server` (any static server works — Live Server, `npx serve`, etc.).
- **Image processing:** When the logo or partner photos need transformation, use Python + Pillow (`PIL`). ImageMagick is NOT installed in this environment.
- **Repo:** `github.com/momentumedia/marketchemica-website` (public). Authenticated via `gh` CLI as `momentumedia`.
- **No environment variables, secrets, .env files, or runtime config.** Site is 100% static.

---

## Repo structure

```
.
├── CLAUDE.md                ← this file
├── README.md
├── .gitignore               ← excludes reference materials + macOS junk
├── index.html               ← Home
├── about.html
├── sectors.html
├── process.html
├── track-record.html
├── team.html                ← contains placeholder bio for Alex Comanita
├── contact.html
└── assets/
    ├── css/
    │   ├── tokens.css       ← color, type, spacing, motion tokens (CSS vars)
    │   └── style.css        ← reset + components + page styles (single file)
    ├── js/
    │   └── main.js          ← mobile nav, scroll reveal, year stamp
    └── img/
        ├── logo.png         ← processed transparent-bg logo
        ├── alex-comanita.jpg
        └── bogdan-comanita.jpg
```

Gitignored but present locally (do not commit, do not delete):

```
MCHA General Presentation.pdf       ← deck — source of truth for bios, deals, clusters
MCHA One-Pager.jpg.jpeg              ← one-pager — taglines, sectors, contact
market chemica logo.jpg              ← original color logo source
Alex Comanita.jpeg                   ← original headshot source
Bogdan Comanita.jpeg                 ← original headshot source
SKILL.md                             ← frontend-design skill spec
marketingskills-main.zip             ← marketing skills bundle (third-party)
```

---

## Local development

```bash
# Run the site locally on http://127.0.0.1:8000
python3 -m http.server 8000

# Or open index.html directly (relative paths work; sticky header behaves correctly)
open index.html
```

- **Install:** none.
- **Build:** none.
- **Test:** none configured. TODO: see *Testing guidance*.
- **Lint/format:** none configured. TODO: consider `prettier` for HTML/CSS if churn increases.
- **Typecheck:** N/A.

---

## Definition of done

Before declaring a change complete:

1. **All 7 pages still load** — `curl` each `*.html` returns 200 and basic layout is intact.
2. **No console errors** in the browser DevTools console on any page.
3. **All 7 page headers/footers match.** If you changed nav, footer, `<title>`, meta tags, or stylesheet links: verify the same edit was applied to all 7 HTML files.
4. **Responsive check at 4 widths:** 375px, 768px, 1024px, 1440px. No horizontal scroll, no overlapping text, mobile drawer opens fully and shows all links.
5. **Open mobile menu** in dev tools mobile mode — every link must be visible and clickable.
6. **Reduced-motion check:** with `prefers-reduced-motion: reduce` enabled, animations are disabled but content remains visible.
7. **Visual QA against design intent.** The site is "Editorial Dealmaker": serif Fraunces display, slate ink + cream paper + molten orange + olive, hex motifs, generous editorial whitespace. Don't drift toward generic AI/SaaS aesthetics.
8. **If pushing to `main`:** verify the live URL serves the new build (`curl -I https://momentumedia.github.io/marketchemica-website/`). Pages takes ~30 sec – 2 min to rebuild.

---

## Coding rules

### CSS

- **Two files only** in `assets/css/`: `tokens.css` (variables) and `style.css` (everything else). Keep this split.
- **Always use `var(--*)`** for color, font, font-size, spacing, motion. If a needed value doesn't exist as a token, **add it to `tokens.css` first**, then reference it.
- **CSS custom properties live on `:root`.** Do not declare `--*` on arbitrary elements unless creating a scoped override (rare).
- **No CSS-in-JS, no preprocessors, no PostCSS.**
- **Naming:** BEM-ish (`.block__element--modifier`). Existing examples: `.nav__link`, `.hero__title`, `.btn--ghost`, `.cta-band--dark`, `.partner__photo-frame`. Keep this style.
- **Media query breakpoints:** `1024px`, `880px`, `540px`. Add new ones only with a strong reason. Mobile-first is *not* the established pattern here — overrides cascade desktop → mobile (max-width queries).
- **`prefers-reduced-motion: reduce`** is honored globally near the top of `style.css`. Any new animation must inherit that reset (which it will if using normal `animation` / `transition`).

### Hex motif system (design language anchor)

- The flat-top hexagon, implemented via `clip-path: polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%)`, is the brand's core visual element. It appears in: sector grid tiles, partner photo frames, partner monogram tiles, and the `Mch` logo itself.
- **Reuse this clip-path.** Do not invent a different hexagon shape (point-top vs flat-top) without a deliberate reason — it would fragment the design system.

### HTML

- **Indent: 2 spaces.** No tabs.
- **Lowercase tags and attributes.**
- **Semantic elements:** `<header>`, `<main>`, `<section>`, `<article>`, `<nav>`, `<footer>`, `<blockquote>`, `<dl>`. The site uses these intentionally — keep them.
- **`<em>` is part of the visual language.** Italics in Fraunces + orange color carry brand meaning. Don't replace `<em>` with `<i>` or styled spans.
- **No inline `style="..."`** for things that already have a class system. The codebase has *some* legacy inline styles; prefer to migrate them into CSS classes when touching that code (see `.cta-band--dark` for the established pattern).

### JavaScript

- **Vanilla only.** No frameworks, no libraries.
- **One file:** `assets/js/main.js`, IIFE-wrapped, defer-loaded.
- **No build, no transpile.** Stick to syntax modern Safari/Chrome/Firefox already support (ES2020+).
- **Add new behavior to `main.js`** unless the file grows past ~200 lines, at which point consider splitting by feature.

### Comments

- **Default to no comments.** The code is small and self-explanatory.
- **Comment only the WHY** when something is non-obvious — e.g. the workaround note for the backdrop-filter / containing-block bug, or why a specific clamp() ramp was chosen.
- **Do not write multi-paragraph docstrings** or `// removed code` markers.

### When to refactor vs patch

- **Patch** for one-off content changes, copy edits, single-page tweaks.
- **Refactor** when the same HTML pattern appears 3+ times across pages — first try to absorb it into a CSS class. If the duplication is structural (e.g. you find yourself editing 7 headers for the same change repeatedly), consider whether it's time to introduce a build step. Don't introduce one casually.

---

## Testing guidance

- **No automated tests today.**
- **Manual verification is the contract** (see *Definition of done*).
- TODO: if the site grows, consider:
  - HTML validation via `validator.w3.org` or a CI workflow.
  - Lighthouse CI (Performance / Accessibility / Best Practices ≥ 95 targets).
  - A Playwright smoke test that loads each page and asserts H1 exists.
- **Do not add testing infrastructure speculatively.** This is a brochure site; tests have a low value-to-overhead ratio until functionality is added (forms, CMS, etc.).

---

## UI/UX rules

### Aesthetic direction

The committed design direction is **Editorial Dealmaker** — magazine-grade typography on a dark slate canvas with molten-orange and olive accents, anchored by hex motifs. Reference: `Monocle` × private-bank pitch book.

- **Display font:** Fraunces (variable). Use the italic with optical-size adjustments for emphasis. Italics + orange = signature brand move.
- **Body font:** Hanken Grotesk. Never Inter.
- **Accent font:** JetBrains Mono. Used for eyebrows, deal tickers, captions, mono labels. ALL CAPS + tracked-out via `letter-spacing: var(--tr-mono)`.
- **Color palette (semantic):**
  - `--ink` (deep slate) is the default canvas
  - `--paper` (warm cream) is for inverted sections (`.section--paper`, `.cta-band` default)
  - `--orange` is the singular hot accent (CTAs, italics, eyebrows, hover)
  - `--olive` is the secondary accent (used sparingly on sector chips, value-chain accents)
- **Cliché bans** (per the originating `frontend-design` SKILL): no purple gradients on white, no Inter/Roboto, no neon-on-black "tech" defaults, no centered hero with rounded buttons.

### Responsiveness

- Layouts collapse: 4 cols → 2 cols → 1 col at the standard breakpoints.
- The mobile nav is a full-screen drawer pinned below the 64px sticky header. **Do not place `backdrop-filter` / `filter` / `transform` on the header or any ancestor of `.nav__list`.**
- Hero typography uses `clamp()` ramps in `tokens.css` — adjust by editing the token, not by adding ad-hoc `font-size` rules at breakpoints.

### Accessibility

- All interactive elements must have visible `:focus-visible` rings (already configured globally — orange 2px outline).
- Color contrast: white/cream on `--ink` passes AA. `--text-mute` on `--ink` is borderline at small sizes — keep mute-text at body size or larger.
- All `<img>` must have meaningful `alt` text or `alt=""` if purely decorative.
- All `<svg>` decorations must have `aria-hidden="true"`.
- Mobile nav must respect `aria-expanded` on the toggle (already wired).

### Loading / empty / error states

- Site is 100% static — no loading or error states currently exist.
- Fonts load with `display=swap` to avoid FOIT.
- TODO: if a contact form is added, define explicit empty/error/success copy.

### Visual consistency

- All 7 pages share the same `.site-header`, `.site-footer`, page-hero rhythm, and section chapter pattern (`<div class="chapter">` with `chapter__num` + `chapter__title`).
- Eyebrow rule pattern (`.eyebrow` with the orange leading bar) is used across every page-hero — preserve it.
- Section chapters number sequentially per page (01, 02, 03…). Don't break the pattern.

---

## API / data rules

- **No backend, no API, no database, no auth.**
- All "data" is hardcoded HTML content sourced from the materials in `MCHA General Presentation.pdf` and `MCHA One-Pager.jpg.jpeg`.
- Email and phone are `mailto:` and `tel:` links to `office@marketchemica.com` / `+1 416 316 7277`. Treat both as canonical — change in **all 7 pages** if updated (search-and-replace approach is fine).
- TODO: if a contact form is added later, document the backend (Formspree, Netlify Forms, custom Worker, etc.) here.

---

## Security & privacy

- **No secrets in code.** None exist today; do not introduce any.
- **No analytics, no tracking pixels** are wired up. If added, document them here and disclose in privacy copy.
- **Contact info is intentionally public** (email + phone are on the existing live site and on this build).
- **Finalis Securities LLC / SEC Rule 15a-6 disclosure** is present in the contact page and footer — do not remove or rephrase without verifying the legally required language with the firm.
- **Do not commit reference materials** (`.pdf`, `.zip`, original JPGs, `SKILL.md`) — already in `.gitignore`. The marketing-skills bundle is third-party copyrighted and must not be redistributed.

---

## Git / workflow rules

- **Default branch:** `main`. There is no `dev`/`staging`/`prod` split — `main` ⇒ production via Pages.
- **Commit style:** present-tense, imperative, sentence case. First line under ~70 chars; body wrapped at ~72 with the *why*. Existing examples in `git log` set the bar.
- **Sign commits** with the existing co-author trailer pattern when working as an agent.
- **Push directly to `main`** is acceptable for content/copy edits and small fixes (this is a brochure site, not an app). For larger reorgs, open a PR — but the site has no CI yet so a PR adds review only, not gating.
- **Do not rewrite unrelated files.** If you're fixing a button, do not also reformat `tokens.css`. Keep diffs focused.
- **Do not modify `.gitignore`** to suddenly start tracking the reference materials. They must stay out of git.
- **Ask before destructive operations:** force push, history rewrite, deleting branches, deleting the repo. None should be needed for normal work.

---

## Review checklist (run before declaring done)

1. Did I touch the header, footer, or `<head>`? → Did I update **all 7 pages**? Run `grep -l "<old-string>"` to find drift.
2. Did I add a new color/font/size/spacing? → Is there a token for it in `tokens.css`?
3. Did I add a `position: fixed` element? → Is there any ancestor with `filter`, `backdrop-filter`, or `transform`?
4. Did I add an `<img>` or `<svg>`? → Does it have correct `alt` / `aria-hidden`?
5. Does the page still look like *Editorial Dealmaker* — or did I drift toward a generic SaaS template?
6. Have I checked it at 375px width with the mobile menu open?
7. Have I verified my change doesn't break existing pages? (`curl -I` each `*.html` after build/push.)
8. Is my commit message specific and the diff focused?

---

## Known pitfalls

- **`backdrop-filter` on the sticky header broke the mobile nav drawer.** It created a containing block for `position: fixed` descendants, collapsing the drawer to ~0px height and showing only the first link. Fix is documented in commit `3c469e7`. Do not reintroduce blur/filter/transform on `.site-header` or any ancestor of `.nav__list`. If you want a glassy header, scope the blur to a child element rather than the header itself.
- **Header / footer / `<head>` duplication across 7 files** is real maintenance debt. Until/unless a build step is introduced, every nav/footer/meta change requires editing 7 files. A Python script in `assets/js/`-adjacent territory or a one-shot sed/awk is the pragmatic move; do not silently desync pages.
- **`team.html` contains placeholder copy for Alex Comanita.** Look for `[Bio pending — to be populated…]` and `<!-- LinkedIn URL pending from user -->`. Replace these *only* with verified info supplied by the client.
- **The `track-record.html` "Israeli Public Company" tombstone has no year** — the source deck didn't include one. The page reads `Mandate · Late-stage DD` instead. Do not invent a year.
- **Deal-type tags on each tombstone** ("Strategic bolt-on · CDMO · Pharma" etc.) were classified by the original author from limited deck info. Verify with the firm before treating any specific phrasing as canonical.
- **`assets/img/logo.png` is a Pillow-processed transparent-background derivative** of `market chemica logo.jpg`. The CSS applies a small `filter: brightness(1.08) saturate(1.05)` so the dark-teal "market" wordmark stays legible on the slate header. If you replace the logo, re-apply (or re-tune) that filter.
- **Inline styles still exist in places** (mostly in CTA bands and section-paper overrides). They predate the `.cta-band--dark` and `.section--paper` patterns. When you touch one of those blocks, prefer to migrate the inline styles into a class. Don't add new ones.
- **GitHub Pages cache is aggressive.** After deploy, hard-refresh or test in a private window. Mobile Safari in particular caches CSS for hours.

---

## TODOs

- Confirm if/when this build replaces `https://www.marketchemica.com/` (DNS / domain ownership / cutover plan).
- Get Alex Comanita's LinkedIn → fill `team.html` placeholder.
- Get LinkedIn URLs for all 5 partners (Bogdan, Alex, Julien, Ron, Paul) → wire into `.partner__links`.
- Confirm process-stage and engagement-model copy with the firm — large portions were authored from inference, not from the deck verbatim.
- Decide on analytics (Plausible, Fathom, GA4) and document here.
- Consider a contact form (Formspree / Netlify Forms / Cloudflare Worker) if `mailto:` proves insufficient.
