# dmartel.dev

Personal portfolio / landing site for Dimitri Martel (DevOps engineer), built with
[Astro](https://astro.build) as a fully static site and deployed to GitHub Pages at
the custom domain `dmartel.dev`. It links out to the sibling blog project at
`blog.dmartel.dev` (built separately) but does not host it.

## Local development

```sh
npm install
npm run dev       # http://localhost:4321
npm run build     # outputs to ./dist
npm run preview   # serve the production build locally
```

Requires Node >= 22.12 (see `engines` in `package.json`).

## Tech stack notes

- Astro `^7.1` (latest stable) with `astro-icon` (Lucide + Simple Icons sets),
  `@astrojs/sitemap`, and `astro-seo`'s `<SEO>` component for meta/OG/Twitter tags. Requires
  Node >= 22.12 (Astro 7 enforces this at build time).
- Styling is Tailwind CSS v4 via the official `@tailwindcss/vite` plugin, **not**
  `@astrojs/tailwind`: that integration's peer dependencies cap at `astro@^3–5` /
  `tailwindcss@^3` and it hasn't been updated for Astro 6/7. Since `astro <=7.0.9` carries
  several patched high-severity XSS/SSRF advisories (fixed only in `7.1.3`+), staying on the
  older stack just to keep `@astrojs/tailwind` wasn't worth it — the sibling `dmartel-blog`
  project was upgraded to match this same stack for the same reason.
- Theme tokens (`--bg`, `--surface`, `--text`, `--text-muted`, `--accent`) live in
  `src/styles/global.css` and are mapped to Tailwind utilities via a CSS `@theme` block
  (Tailwind v4's native, config-file-free approach), so `bg-bg`, `text-accent`, `font-mono`,
  etc. all resolve to those custom properties.
- View transitions use `<ClientRouter />` from `astro:transitions` (the current name;
  `<ViewTransitions />` is its deprecated alias, removed as of Astro 6).
- Fonts are self-hosted via `@fontsource/inter` and `@fontsource/jetbrains-mono` — no Google
  Fonts or other external font CDN.
- The light-theme accent color was darkened one step from the originally specified `#0d9488`
  to `#0f766e` (same hue): the original measured ~3.6:1 contrast against the light background,
  under the WCAG AA 4.5:1 threshold for normal text. `#0f766e` measures ~5.2:1. Dark mode's
  accent (`#2dd4bf`) was left untouched — it already passes at ~10:1.
