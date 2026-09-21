# ErgoTools · Calculadoras RULA y REBA

Static, single-file web tools for the **Taller de Ergonomía e Ingeniería de Métodos** course.
Each tool scores a posture with an observational method and produces the final score, the action
level and a printable/copyable report:

| Tool | Method | Scores | URL |
| --- | --- | --- | --- |
| **RULA** | [Rapid Upper Limb Assessment](https://en.wikipedia.org/wiki/Rapid_upper_limb_assessment) — upper limbs (arm, forearm, wrist, twist) + neck, trunk, legs, muscle use and force | final 1–7 | <https://tavobula.github.io/ErgoTools/> |
| **REBA** | [Rapid Entire Body Assessment](https://en.wikipedia.org/wiki/Rapid_Entire_Body_Assessment) — whole body (neck, trunk, legs, both sides' arm/forearm/wrist), load/force, coupling and muscular activity | final 1–15 | <https://tavobula.github.io/ErgoTools/reba-calculadora.html> |

Both tools are cross-linked from the header of each page (the `RULA` / `REBA` switcher), so you can
move between them without typing a URL.

**Live site:** <https://tavobula.github.io/ErgoTools/>

No build step, no dependencies, no backend — each tool is self-contained (HTML, CSS and JavaScript
in one file). The only external resource is the IBM Plex font from Google Fonts, and the tools
degrade to system fonts if it cannot be reached. The favicon is an inline SVG data URI, so there is
no extra request and no `/favicon.ico` 404.

## Why it did not work before

GitHub Pages was already enabled for this repository (source: branch `main`, folder `/`), but the
site returned GitHub's generic **"404 · File not found"** at
`https://tavobula.github.io/ErgoTools/`.

Reason: Pages looks for an `index.html` at the root of the published folder, and the only file in
the repository was `rula-calculadora.html`. The app was reachable *only* through its full filename,
so the site URL itself was broken.

The REBA tool was added later as `reba-calculadora.html`. It was served correctly at its full
filename, but it was an **orphan page**: nothing in the site linked to it, it did not link back, it
had no favicon (so every visit fired a 404 request for `/ErgoTools/favicon.ico`) and no meta
description or Open Graph tags. It is now a first-class page of the site.

## What changed

| File | Purpose |
| --- | --- |
| `index.html` | The RULA tool, renamed from `rula-calculadora.html` so Pages serves it at the site root. Git tracks it as a rename, so history is preserved. Added a favicon (inline SVG data URI), a meta description, `color-scheme`/`theme-color`, Open Graph tags and the `RULA` / `REBA` switcher in the header. |
| `reba-calculadora.html` | The REBA tool. Added the same head metadata as `index.html` (inline SVG favicon, meta description, `color-scheme`/`theme-color`, Open Graph) and the `RULA` / `REBA` switcher in the header, so the page is reachable from the site and stops requesting a missing `/favicon.ico`. |
| `.nojekyll` | Tells Pages to publish the files as-is instead of running them through Jekyll/Liquid. The site is plain static HTML, so this removes a build step and avoids any chance of Jekyll rewriting markup. |
| `rula-calculadora.html` | Tiny redirect stub → `index.html`. This deep URL is the *only* one that worked originally (`https://tavobula.github.io/ErgoTools/rula-calculadora.html`), so it is kept alive instead of being deleted: bookmarks, shared links and course material pointing at it keep working. |
| `404.html` | Custom, on-brand 404 (replaces GitHub's generic one). It resolves the site base path itself, so it works even when the missing URL is nested, and offers a link to *both* calculators plus an automatic redirect back to the RULA tool. |
| `README.md` | This file. |

### Sub-path safety

A project site is published under `/<repository>/`, not at the domain root, so relative and
absolute URLs matter:

- every asset in the tools is either inline or an absolute `https://` URL (Google Fonts) — nothing
  breaks under the sub-path;
- the `RULA` / `REBA` switcher and the redirect stub use **relative** hrefs (`index.html`,
  `reba-calculadora.html`), which resolve correctly at any depth, including on a custom domain;
- `404.html` computes the base path at runtime (`REPO` constant at the top of its script) because a
  404 is rendered at the *requested* URL, where relative links would point to the wrong folder.

If the repository is ever renamed, or published from a user site / custom domain, update the `REPO`
constant in `404.html` (set it to `''` for a domain root).

## Deploying

Nothing to do. Pages is configured as **"Deploy from a branch" → `main` → `/ (root)`**, so every
push to `main` republishes the site automatically (usually live within a minute; check
*Settings → Pages* or the `pages-build-deployment` status for progress).

To publish a change:

```bash
git switch main
git merge <your-branch>
git push origin main
```

Because Pages publishes from `main`, changes on a feature branch are **not** live until they are
merged into `main`.

### Optional: switch to GitHub Actions

The branch-based ("legacy") publishing used here needs no workflow. If you prefer Actions
(build/deploy on push with an `environment: github-pages` deployment record), switch
*Settings → Pages → Build and deployment → Source* to **GitHub Actions** and add the
[`Static HTML` starter workflow](https://github.com/actions/starter-workflows/blob/main/pages/static.yml)
as `.github/workflows/pages.yml` with `path: '.'`.

Do **not** add that workflow while the source is still "Deploy from a branch": `actions/deploy-pages`
fails unless the Pages source is set to GitHub Actions, and every push would show a red ❌.

## Running it locally

Any static file server works — open the folder and serve it:

```bash
# Python
python3 -m http.server 8080

# or Node
npx serve .
```

Then browse to <http://localhost:8080/> (RULA) or
<http://localhost:8080/reba-calculadora.html> (REBA). Opening a file directly with `file://` also
works; the tools keep all state in memory and do not call any API.

To reproduce the exact Pages layout (site under a sub-folder), copy the files into a directory
named `ErgoTools/` and serve its parent: <http://localhost:8080/ErgoTools/>.

## Scoring tables

The REBA tables in `reba-calculadora.html` (`TABLE_A`, `TABLE_B`, `TABLE_C` and the action levels)
follow Hignett & McAtamney (2000) as published by
[Ergonautas / Universitat Politècnica de València](https://www.ergonautas.upv.es/ergoniza/app_en/land/index.html?method=reba):
Table A is indexed `[trunk-1][(neck-1)*4 + (legs-1)]` because the legs score runs 1–4 (base support
score plus the knee-flexion adjustment), Table B `[arm-1][(forearm-1)*3 + (wrist-1)]`, and Table C
`[A-1][B-1]`. Group A is exhaustive over all 60 posture combinations (192 including the trunk/neck
adjustments) and Group B over all 36 (64 including the arm/wrist adjustments).

## Repository layout

```
.
├── index.html              # the RULA calculator (whole app: markup + CSS + JS)
├── reba-calculadora.html   # the REBA calculator (whole app: markup + CSS + JS)
├── rula-calculadora.html   # redirect stub for legacy links
├── 404.html                # custom 404 page (links both tools)
├── .nojekyll               # publish as-is, skip Jekyll
└── README.md
```
