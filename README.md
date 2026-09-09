# The Belgian Runners

Source for [The Belgian Runners](https://thebelgianrunners.com/) — the
online home of Kelly and Nills, two Belgian runners living in California.
The landing page introduces us, sends runners to Instagram, and makes it easy for
fellow runners, brands and race organizers to get in touch.

Built with [Hugo](https://gohugo.io) and the [Congo](https://github.com/jpanther/congo)
theme. Fully static — Markdown content, no CMS, no database, no JavaScript framework.

The canonical URL is **https://thebelgianrunners.com/**, with `www` redirecting to
the apex domain once DNS and HTTPS provisioning are complete. Hosting uses GitHub
Pages. See [Custom domain and HTTPS](#custom-domain-and-https) below for setup and
troubleshooting.

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
hugo server -D --baseURL http://localhost:1313/thebelgianrunners/
```

This starts a live-reloading local server at `http://localhost:1313/thebelgianrunners/` and
includes draft content (`-D`). Leave off `-D` to preview the site exactly as it will
appear once published.

## Project structure

```
content/
├── _index.md          # homepage copy, hero/contact metadata and short About introduction
├── about/_index.md     # About page
└── posts/              # "Stories" — race reports, gear reviews, training, adventures
    └── _index.md
config/_default/        # site configuration (Congo theme config, copied on install)
layouts/_partials/home/custom.html   # compact homepage overriding Congo
layouts/_partials/responsive-photo.html # responsive WebP photos with intrinsic dimensions
layouts/_partials/header/custom.html # always-visible responsive navigation
layouts/_partials/footer.html       # footer with contact and back-to-top links
layouts/posts/list.html             # Stories list and intentional empty state
layouts/simple.html                # readable About layout
layouts/404.html                   # recovery links for missing pages
assets/
├── css/custom.css       # brand styling for the custom homepage (see note below)
├── css/schemes/trailhead.css  # custom "trailhead" colour scheme for the theme
└── images/              # site photography
themes/congo/            # Congo theme, included as a git submodule
.github/workflows/hugo.yml  # build & deploy to GitHub Pages
```

## Publishing new content

The homepage is intentionally a short online introduction, not an empty publication.
Edit its wording in `content/_index.md`: the front matter contains the hero and contact
copy, and the Markdown body contains the short About introduction. The full personal
story lives in `content/about/_index.md`.

The Stories navigation link and the homepage's latest-stories section appear
automatically when a non-draft post is published. Until then, `/posts/` remains
available with a friendly empty state, but isn't promoted on the homepage.

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

The homepage, header, footer, About, Stories and 404 layouts are small project-level
overrides, styled in `assets/css/custom.css`. Article, taxonomy and feed templates
continue to use Congo. The theme submodule itself is not modified.

Photography is served as responsive, compressed WebP with explicit dimensions to
reserve space while loading. The hero is prioritized; supporting photos are lazy-loaded.
System sans-serif and Georgia heading fonts avoid third-party font requests. Navigation
works without JavaScript, with visible keyboard focus, current-page indicators and
touch-sized targets; smooth scrolling respects reduced-motion preferences.

> **Note on styling:** Congo ships a *precompiled* Tailwind CSS bundle containing only
> the utility classes the theme itself uses. Rather than adding an npm/Tailwind build
> step (and the added maintenance overhead) just for a handful of homepage sections,
> `assets/css/custom.css` uses small, plain, semantic CSS classes (prefixed `tbr-`) for
> the custom layouts, alongside Congo's built-in styles.

## Deployment

Pushing to `main` triggers `.github/workflows/hugo.yml`, which:

1. Checks out the repo (including the Congo submodule)
2. Installs Hugo
3. Builds the site with `hugo --minify`, using GitHub's Pages configuration to set the
   correct base URL automatically (including the configured custom domain), with
   HTTPS canonical URLs even while certificate provisioning is pending
4. Publishes the result to GitHub Pages

The very first time this repo is set up, GitHub Pages needs to be pointed at "GitHub
Actions" as its source: **Settings → Pages → Build and deployment → Source → GitHub
Actions**.

### Custom domain and HTTPS

Associate the domain with GitHub Pages **before** pointing DNS at GitHub. In the
repository's **Settings → Pages**, keep **GitHub Actions** as the publishing source
and set the custom domain to `thebelgianrunners.com`. The old GitHub Pages URL
redirects to this domain; temporary unavailability is expected during DNS cutover.

Account-level ownership verification is a separate recommended protection against
domain takeover. In the owner's personal **GitHub Settings → Pages**, add and
verify the domain using the exact TXT record GitHub provides. Keep that record
after verification. Repository domain association does not prove account-level
ownership verification, and no verification token is stored in this repository.

In Namecheap's DNS dashboard, configure these website records (automatic/default
TTL is fine):

| Type | Host | Value |
| --- | --- | --- |
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `nillsf.github.io` |

The `www` target is a hostname, with no protocol or repository path. GitHub Pages
redirects `www.thebelgianrunners.com` to the configured apex domain when both are
correctly pointed at Pages. Replace conflicting parking/URL redirect or website
records for `@` and `www`; do not leave stale A or AAAA records pointing elsewhere.
Do not alter unrelated TXT records or email settings.

After DNS propagates and GitHub provisions the certificate, enable **Enforce HTTPS**
in the repository's **Settings → Pages**. If the DNS check fails or HTTPS is not
available, check the exact website records above, conflicting records, and any CAA
restrictions that could prevent GitHub's Let's Encrypt certificate issuance.
Certificate provisioning may take up to 24 hours after correct DNS is visible.
Do not remove the Pages domain association simply because DNS or the certificate
is still pending, and do not bypass browser TLS warnings.

This repository uses a custom GitHub Actions publishing workflow: GitHub ignores
and does **not require** a `CNAME` file in its artifact. Do not add `static/CNAME`;
manage the custom domain in Pages settings. `config/_default/hugo.toml` uses
`https://thebelgianrunners.com/` for production builds, while the workflow reads the
Pages base URL through `actions/configure-pages`. The explicit localhost override
in [Local development](#local-development) keeps previews on your machine.

Namecheap email forwarding is independent of website hosting and uses its own
MX/SPF records. Preserve those records and manage forwarding destinations privately
in Namecheap; the site's public contact address remains
[hello@thebelgianrunners.com](mailto:hello@thebelgianrunners.com).

See GitHub's [custom domain documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
for current DNS, verification and HTTPS guidance.
