# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The public website for the Coordinamento delle Sezioni Piemontesi dell'Associazione Nazionale Alpini — Protezione Civile (CAP ANA Piemonte), served at coordanapiemonte.it via GitHub Pages (see `CNAME`). Italian-language site.

## Architecture

The entire site is **one file**: `index.html`. There is no build step, no package.json, no bundler, no framework — it's a hand-rolled single-page app in vanilla JS/CSS embedded directly in the HTML.

- **Routing**: `goTo(page)` sets `location.hash` and calls `render(page)`, which swaps the contents of `<main id="app">`. Valid pages are declared in the `PAGES` array (`home`, `chi-siamo`, `organico`, `contributi`). A `hashchange` listener and `init()` handle direct links/back-forward navigation.
- **Rendering**: each page has a `renderX()` function that returns an HTML template string (no virtual DOM/diffing — plain string interpolation).
- **Data lives inline as JS constants** near the top of the `<script>` block, not in separate JSON/data files:
  - `ORGANICO` — board members (consiglio direttivo, coordinatore, revisori dei conti), rendered as cards via `orgCard()`.
  - `CONTRIBUTI` — list of yearly PDF reports (anno, label, file url), rendered by `renderContributi()`.
- **Logo**: embedded directly as a base64 data URI in the JS (`const LOGO_B64 = '...'`, one very long line near line 196) rather than as a static asset — set as `img.src` at init time.
- **Static assets**: `img/` holds hero/strip photos and partner logos (PNG/JPG); `contributi/` holds the yearly PDF reports (`2023.pdf`, `2024.pdf`, `2025.pdf`) linked from the Contributi page.

## Adding/updating content

- **New yearly contribution PDF**: drop the file in `contributi/` and add a corresponding entry to the `CONTRIBUTI` array in `index.html` (see the comment above that array) — `url` must match the committed filename.
- **Board/organico changes**: edit the `ORGANICO` object directly (three groups: `consiglio`, `coordinatore`, `revisori`).
- **New page**: add an entry to `PAGES`, add a branch in `render()`, and write a corresponding `renderX()` function following the existing template-string style.

## Running/testing locally

No build or test tooling exists. Open `index.html` directly in a browser, or serve the directory with any static file server (e.g. `python -m http.server`) to test relative asset paths (`img/`, `contributi/`) correctly.
