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

## Before you deploy

GitHub (`DimMarr`) and email (`dimitri.martel@proton.me`) are already filled in. One
placeholder remains — search the repo for `TODO_` to find it again after editing:

- `src/components/Projects.astro:3` — second project card (name + `TODO_PROJECT_URL` + real description, replacing the "TODO: describe project" placeholder text)
- `src/components/Projects.astro:4` — third project card (same as above)

Also worth doing once real values are in:

- Point Plausible analytics at your instance: set `PUBLIC_PLAUSIBLE_DOMAIN` (e.g. in the
  GitHub Actions workflow's `env:` or repo/environment secrets) to enable the snippet in
  `src/layouts/BaseLayout.astro`. It's gated off by default since no analytics endpoint
  exists yet — no fake URL is hardcoded.

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

## DNS setup for the apex domain

Point `dmartel.dev` at GitHub Pages with four **A** records at the apex (`@`):

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

Optionally, add **AAAA** records at the apex for IPv6:

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

Then, in the GitHub repo's **Settings → Pages**:

1. Set the custom domain to `dmartel.dev`. GitHub will manage/verify the `public/CNAME`
   file for you on its side — the one committed in this repo is kept too so the custom
   domain survives a fresh Pages deploy even before GitHub rewrites it.
2. Once DNS has propagated and GitHub has issued a certificate, enable **Enforce HTTPS**.

## Deployment

`.github/workflows/deploy.yml` builds and deploys to GitHub Pages on every push to `main`
(via `actions/configure-pages`, `actions/upload-pages-artifact`, `actions/deploy-pages`).
No extra repo secrets are required beyond having GitHub Pages enabled with source
"GitHub Actions".
