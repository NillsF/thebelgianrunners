# The Belgian Runners

Source for [thebelgianrunners.com](https://nillsf.github.io/thebelgianrunners/) — a
running-focused website by Nills and Kelly: two Belgian runners living in California,
sharing race reports, training notes, gear reviews and trail adventures.

Built with [Hugo](https://gohugo.io) and the [Congo](https://github.com/jpanther/congo)
theme. Fully static — Markdown content, no CMS, no database, no JavaScript framework.

> **Note:** `thebelgianrunners.com` hasn't been purchased yet. The site currently
> deploys to the free default GitHub Pages URL:
> **https://nillsf.github.io/thebelgianrunners/**. See
> [Switching to the custom domain later](#switching-to-the-custom-domain-later) below
> for the steps to move to the real domain once it's bought.

## Requirements

- [Hugo](https://gohugo.io/installation/) **extended**, v0.158.0 or later
- [Git](https://git-scm.com/) (the Congo theme is included as a git submodule)

## Getting the code

```sh
git clone --recurse-submodules https://github.com/nillsf/thebelgianrunners.git
cd thebelgianrunners
```

If you already cloned without `--recurse-submodules`, fetch the theme with:

```sh
git submodule update --init --recursive
```

## Local development

```sh
hugo server -D
```

This starts a live-reloading local server (usually at `http://localhost:1313/`) and
includes draft content (`-D`). Leave off `-D` to preview the site exactly as it will
appear once published.

## Project structure

```
content/
├── _index.md          # homepage front matter (see layouts/_partials/home/custom.html)
├── about/_index.md     # About page
└── posts/              # "Stories" — race reports, gear reviews, training, adventures
    └── _index.md
config/_default/        # site configuration (Congo theme config, copied on install)
layouts/_partials/home/custom.html   # custom homepage (hero, sections) overriding Congo
assets/
├── css/custom.css       # brand styling for the custom homepage (see note below)
├── css/schemes/trailhead.css  # custom "trailhead" colour scheme for the theme
└── images/              # site photography
themes/congo/            # Congo theme, included as a git submodule
.github/workflows/hugo.yml  # build & deploy to GitHub Pages
```

## Publishing new content

Every long-form piece (race report, gear review, training article, adventure story)
lives under `content/posts/` as a Markdown file. Clean URLs like
`/posts/javelina-100-2025/` are generated automatically — nothing needs to be hardcoded
into any template.

Create a new post:

```sh
hugo new posts/javelina-100-2025.md
```

This uses the `archetypes/posts.md` template, which pre-fills the front matter fields:

```toml
+++
title = 'Javelina 100 2025'
date = '2025-11-02T10:00:00-07:00'
description = ''
categories = []
tags = []
image = ''
draft = true
+++
```

- **title** — page title
- **date** — controls sort order on the Stories page
- **description** — used for SEO/social previews and the story summary
- **categories** — e.g. `["Race Reports"]`, `["Gear"]`, `["Training"]`, `["Adventure"]`
  (categories automatically get their own list page, e.g. `/categories/race-reports/`)
- **tags** — free-form tags
- **image** — optional hero/feature image for the article
- **draft** — set to `false` (or remove the line) when you're ready to publish; draft
  posts are excluded from production builds

Write the article body in Markdown below the front matter, then commit and push to
`main` — the GitHub Actions workflow builds and deploys automatically.

## Theme: why Congo?

[Congo](https://github.com/jpanther/congo) was chosen after comparing several
actively-maintained, lightweight Hugo themes because it offers:

- A photography-friendly, customisable homepage and article layouts — important since
  this site is built around real trail-running photography rather than stock imagery.
- A clean, **light** default appearance with an easily themeable colour system (see
  `assets/css/schemes/trailhead.css`, a custom warm-orange / California-sky-blue palette
  applied on top of Congo's neutral base).
- Built-in taxonomies, RSS, sitemap, Open Graph/SEO metadata and multilingual support —
  everything needed for the site to grow into race reports, gear reviews, training
  articles and taxonomy pages without extra plumbing.
- A precompiled CSS bundle and minimal JavaScript — no Node/Tailwind build step is
  required just to run the site (see note below on customising styles).
- Active maintenance and clear documentation.

The homepage itself is fully custom (`layouts/_partials/home/custom.html`), styled with
its own small stylesheet (`assets/css/custom.css`) so the site feels specifically like
The Belgian Runners rather than a generic Hugo demo, while every other page (About,
Stories, taxonomies, 404, RSS) uses Congo's stock, well-tested layouts.

> **Note on styling:** Congo ships a *precompiled* Tailwind CSS bundle containing only
> the utility classes the theme itself uses. Rather than adding an npm/Tailwind build
> step (and the added maintenance overhead) just for a handful of homepage sections,
> `assets/css/custom.css` uses small, plain, semantic CSS classes (prefixed `tbr-`) for
> the custom homepage. Everything else relies on Congo's built-in styles as normal.

## Deployment

Pushing to `main` triggers `.github/workflows/hugo.yml`, which:

1. Checks out the repo (including the Congo submodule)
2. Installs Hugo
3. Builds the site with `hugo --minify`, using GitHub's Pages configuration to set the
   correct base URL automatically (so this works unchanged for the current
   `github.io` URL and, later, for the custom domain)
4. Publishes the result to GitHub Pages

The very first time this repo is set up, GitHub Pages needs to be pointed at "GitHub
Actions" as its source: **Settings → Pages → Build and deployment → Source → GitHub
Actions**.

### Switching to the custom domain later

Once `thebelgianrunners.com` is purchased:

1. Point its DNS at GitHub Pages (an `ALIAS`/`ANAME`/`A` record for the apex domain,
   plus optionally a `CNAME` record for `www` pointing at `nillsf.github.io`). See the
   [GitHub Pages custom domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
   for current recommended record values.
2. Add a `static/CNAME` file to this repo containing exactly:
   ```
   thebelgianrunners.com
   ```
3. In the repo's **Settings → Pages**, set the custom domain to
   `thebelgianrunners.com` and enable **Enforce HTTPS** once it's available.
4. Optionally update `baseURL` in `config/_default/hugo.toml` to
   `https://thebelgianrunners.com/` for local builds (the GitHub Actions workflow
   already detects the correct URL automatically via `actions/configure-pages`).

No other changes are required — the site's internal links all use Hugo's relative URL
helpers, so they adapt automatically to whichever base URL is configured.
