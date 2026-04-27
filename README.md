# Market Chemica Associates — Website

Specialized M&A advisory and management consulting for fine and specialty chemicals.
Source for [marketchemica.com](https://www.marketchemica.com/).

## Stack

Hand-crafted static site — no framework, no build step.

- HTML5 across 7 pages: `index`, `about`, `sectors`, `process`, `track-record`, `team`, `contact`
- CSS in `assets/css/` (`tokens.css` + `style.css`)
- Vanilla JS in `assets/js/main.js`
- Google Fonts: Fraunces (display) · Hanken Grotesk (body) · JetBrains Mono (accent)

## Local development

```bash
python3 -m http.server 8000
# → http://127.0.0.1:8000
```

Or open `index.html` directly in a browser.

## Deploy

Static files only. Deploy the repository root to any static host:

- **GitHub Pages** — enabled on `main` branch, `/` (root)
- **Netlify / Vercel / Cloudflare Pages** — connect repo, no build command, publish directory is the repo root

## File structure

```
.
├── index.html
├── about.html
├── sectors.html
├── process.html
├── track-record.html
├── team.html
├── contact.html
└── assets/
    ├── css/{tokens.css, style.css}
    ├── js/main.js
    └── img/{logo.png, alex-comanita.jpg, bogdan-comanita.jpg}
```

## License

© Market Chemica Associates Inc. All rights reserved.
