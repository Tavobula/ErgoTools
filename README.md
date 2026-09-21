# ErgoTools · Calculadora RULA

Static, single-file web tool for the **Taller de Ergonomía e Ingeniería de Métodos** course.
It scores a posture with the [RULA](https://en.wikipedia.org/wiki/Rapid_upper_limb_assessment)
method (Rapid Upper Limb Assessment): group A (arm, forearm, wrist, twist), group B (neck,
trunk, legs), muscle use and force, then the final 1–7 score, the action level and a
printable/copyable report.

**Live site:** <https://tavobula.github.io/ErgoTools/>

No build step, no dependencies, no backend — everything (HTML, CSS and JavaScript) lives in
`index.html`. The only external resource is the IBM Plex font from Google Fonts, and the tool
degrades to system fonts if it cannot be reached.

## Why it did not work before

GitHub Pages was already enabled for this repository (source: branch `main`, folder `/`), but the
site returned GitHub's generic **"404 · File not found"** at
`https://tavobula.github.io/ErgoTools/`.

Reason: Pages looks for an `index.html` at the root of the published folder, and the only file in
the repository was `rula-calculadora.html`. The app was reachable *only* through its full filename,
so the site URL itself was broken.

## What changed

| File | Purpose |
| --- | --- |
| `index.html` | The tool, renamed from `rula-calculadora.html` so Pages serves it at the site root. Git tracks it as a rename, so history is preserved. Added a favicon (inline SVG data URI, so no extra request and no `/favicon.ico` 404), a meta description, `color-scheme`/`theme-color` and Open Graph tags. |
| `.nojekyll` | Tells Pages to publish the files as-is instead of running them through Jekyll/Liquid. The site is plain static HTML, so this removes a build step and avoids any chance of Jekyll rewriting markup. |
| `rula-calculadora.html` | Tiny redirect stub → `index.html`. This deep URL is the *only* one that works today (`https://tavobula.github.io/ErgoTools/rula-calculadora.html`), so it is kept alive instead of being deleted: bookmarks, shared links and course material pointing at it keep working. |
| `404.html` | Custom, on-brand 404 (replaces GitHub's generic one). It resolves the site base path itself, so it works even when the missing URL is nested, and offers a redirect back to the calculator. |
| `README.md` | This file. |

### Sub-path safety

A project site is published under `/<repository>/`, not at the domain root, so relative and
absolute URLs matter:

- every asset in `index.html` is either inline or an absolute `https://` URL (Google Fonts) — nothing
  breaks under the sub-path;
- the redirect stub uses the relative `index.html`, which resolves correctly at any depth;
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

Then browse to <http://localhost:8080/>. Opening `index.html` directly with `file://` also works;
the tool keeps all state in memory and does not call any API.

To reproduce the exact Pages layout (site under a sub-folder), copy the files into a directory
named `ErgoTools/` and serve its parent: <http://localhost:8080/ErgoTools/>.

## Repository layout

```
.
├── index.html              # the RULA calculator (whole app: markup + CSS + JS)
├── rula-calculadora.html   # redirect stub for legacy links
├── 404.html                # custom 404 page
├── .nojekyll               # publish as-is, skip Jekyll
└── README.md
```
