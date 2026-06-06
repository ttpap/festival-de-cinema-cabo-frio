# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this is

Marketing website for **FINCCA — Festival Internacional de Cinema de Cabo Frio**
(1ª edição, 14–17 May 2026, Cabo Frio, RJ, Brazil). It is a single static page
in Brazilian Portuguese (`<html lang="pt-BR">`), deployed on Vercel.

There is **no build step, no framework, no package manager, and no
dependencies.** The entire site is one HTML file plus static image/PDF assets.

## Layout

```
index.html      The whole site: markup + inline <style> + inline <script>
serve.js        Zero-dependency Node static file server (local preview only)
.claude/
  launch.json   Dev launch config (runs serve.js on port 3333)
.gitignore      Ignores .vercel
*.png .jpg      Logos, sponsor bars, winner stills, textures
*.pdf           Catálogo, programação, chamamento/edital, vencedores
```

### index.html structure (~2550 lines)

- **`<head>`** (lines 1–28): title, favicon, Open Graph + Twitter meta
  (canonical domain `https://fincca.com.br`), Google Fonts preconnect
  (`Playfair Display` for headings, `Inter` for body).
- **`<style>`** (lines ~29–1729): all CSS lives inline here. Design tokens are
  CSS custom properties under `:root` (line 40) — `--primary-dark`,
  `--primary-teal`, `--accent-turquoise`, `--accent-beige`, `--text-dark`,
  `--text-light`, `--bg-light`. Reuse these instead of hardcoding colors.
- **Body** (lines ~1730–2508): a fixed `<header>`/`<nav>` followed by content
  `<section>`s, each with an `id` used by the nav anchor links:
  `#sobre`, `#programacao`, `#catalogo`, `#edital`, `#premiacoes` (Vencedores),
  `#atividades`, `#contato`. Other unanchored sections: hero, stats, venues,
  apoiadores (sponsors), termo-fomento.
- **`<script>`** (lines ~2510–2551): plain vanilla JS, no libraries. Two
  responsibilities: (1) the catalog PDF modal (`openCatalog`/`closeCatalog`,
  lazy-loads `catalogo-fincca-2026.pdf` into an `<iframe>`, closes on
  overlay-click or Escape); (2) the mobile hamburger menu toggle.

## Running locally

```bash
node serve.js              # serves repo root at http://127.0.0.1:8080
node serve.js 3333         # custom port (positional arg)
PORT=3333 node serve.js    # custom port (env var)
```

`serve.js` maps common MIME types, sends `Cache-Control: no-store` for HTML and
`Content-Disposition: inline` for PDFs, and 404s anything missing. It is for
preview only — production is served as static files by Vercel.

There are **no tests, linters, or CI** in this repo.

## Conventions

- **Language:** all user-facing copy is Brazilian Portuguese. Match the tone and
  spelling of surrounding content.
- **Commit messages:** written in Portuguese, often with conventional-commit
  prefixes (`feat:`, `fix:`) but plain descriptive messages are also common.
  Follow the existing style.
- **Assets** live flat in the repo root and are referenced by relative path
  (e.g. `src="logo-fincca1.png"`). When adding an image/PDF, drop it in the root
  and reference it relatively. Logo/sponsor files follow a `logo-*.png` naming
  pattern; award stills follow `vencedor-*.png`.
- **Styling/JS:** keep CSS in the inline `<style>` block and JS in the inline
  `<script>` block — do not introduce external CSS/JS files or build tooling
  unless explicitly asked. Prefer existing CSS variables and class patterns.
- **Content updates** (programação, vencedores, edital, etc.) typically mean
  editing the relevant `<section>` in `index.html` and sometimes swapping a
  linked PDF. Festival data (dates, film counts, categories, venues) recurs in
  several places — update them consistently.

## Deployment

Hosted on **Vercel** (the `.vercel` directory is gitignored). No config file is
committed; Vercel serves the repo as a static site. Pushing to the default
branch is what publishes — there is no separate build/deploy script here.
