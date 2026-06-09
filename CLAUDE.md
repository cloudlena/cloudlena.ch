# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Personal speaker website for Lena Fuhrimann (cloudlena.ch), hosted on GitHub Pages. A single self-contained HTML file: `index.html`, hosted at the root.

## Architecture

`index.html` is a plain, self-contained HTML/CSS/JS file — no build step, no framework, no bundler. Open it directly in a browser.

- **HTML**: Static structure — topbar, hero, controls (filter tabs + search input), feed container, footer.
- **CSS**: Inlined in `<style>`. TokyoNight-inspired dark palette. Fira Code loaded from Google Fonts.
- **Data**: `TYPES` and `FEED` arrays in a `<script>` block at the bottom of the file. Add or update feed entries there.
- **JS**: ~80 lines of vanilla JS (same `<script>` block). Renders feed items, handles filter/search, IntersectionObserver for scroll-in animations, keyboard shortcuts (`/` focuses search, `Esc` clears).

## Design tokens

Dark palette: `#1a1b26` background, `#c0caf5` primary text, `#bb9af7` accent purple, `#9ece6a` green. Display font is Fira Code (monospace); terminal-style `lena_` branding.

## Local preview

Open `index.html` directly in a browser — no server needed.

## Deployment

GitHub Pages serves the file directly from `index.html` at the root URL.

## Adding content

Edit the `FEED` array in the `<script>` block. Each entry has:
- `t`: type — `"talk"`, `"pod"`, `"post"`, or `"oss"`
- `title`, `where`, `city`, `date` (YYYY-MM), `meta`, `url`, `desc`
- `thumb`: YouTube thumbnail URL (use `yt("videoId")`) or `null`
