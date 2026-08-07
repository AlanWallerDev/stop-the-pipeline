# stop-the-pipeline

A single-page public notice about the proposed West Coast Oil Pipeline designation
under the Building Canada Act, aimed at residents of Kamloops and the Thompson
Valley. It carries a live countdown to the close of the federal comment period
(September 18, 2026), the case against the designation, and contact details for
the federal, provincial, and municipal representatives who can put a comment on
the record.

Live at **https://alanwallerdev.github.io/stop-the-pipeline/**

## How it works

Everything is static. `index.html` is a template rendered client-side by
`support.js`, a small runtime that loads React and evaluates the
`<script type="text/x-dc">` block at the bottom of the page — that block holds
the countdown timer, the contact list, and the filter state. Styling comes from
the design system in `_ds/`.

The page makes **no third-party requests**. As exported, the runtime fetched
React from unpkg and the design system pulled Archivo from Google Fonts; both are
now served from this repo instead:

- React and React DOM 18.3.1 live in `vendor/`, redirected via the runtime's own
  `window.__resources` hook in the `<head>` of `index.html`. The files are
  byte-identical to unpkg's (verified against the SRI hashes pinned in
  `support.js`).
- Archivo is self-hosted in `_ds/modernist-*/fonts/`, with the `@font-face`
  rules in `_ds/modernist-*/fonts.css`.

Babel is not vendored because this page has no JSX modules to transpile, so the
runtime never requests it. If you later add an `x-import` of a `.jsx` module, it
will fall back to fetching Babel from unpkg.

There is no build step and no dependencies to install. Open `index.html` in a
browser, or serve the directory:

```sh
python3 -m http.server 8000
```

## Layout

| Path | What it is |
| --- | --- |
| `index.html` | The page: markup, template bindings, and the component script |
| `support.js` | Client-side runtime that renders the template |
| `_ds/modernist-*/` | Design system — CSS custom properties, type scale, components |
| `vendor/` | React + React DOM, served locally instead of from a CDN |
| `uploads/` | The printable handout PDF |
| `preview.webp` | Social share image referenced by the Open Graph tags |
| `.nojekyll` | Stops Pages from dropping the `_ds/` directory (Jekyll skips leading-underscore paths) |

## Editing

- **The deadline** is set in two places in `index.html`: the `deadline` prop
  default in the `data-props` attribute, and the fallback in the `new Date(...)`
  call in the component script. Change both.
- **Contacts** live in the `all` array near the bottom of `index.html`.
- **Statistics** (wildfire, cost, and carbon figures) are in the arrays just
  above it, with their sources listed in the citations block at line ~198.

## Deployment

Pages is configured to **deploy from a branch** — `main`, root. Pushing to
`main` is the whole deploy: GitHub's own `pages-build-deployment` publishes the
repository as-is, with no workflow of ours in the loop and no build step.

`.nojekyll` matters here. Without it Jekyll would strip the `_ds/` directory,
taking the entire design system with it.
