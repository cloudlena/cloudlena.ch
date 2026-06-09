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
- `thumb`: path to a locally downloaded thumbnail in `images/` (e.g. `images/yt-<videoId>.webp`) or `null`

`FEED` is rendered in array order without any sorting — entries must already be in date-descending order when added. Insert new entries at the position matching their `date`, don't just append.

Whenever an entry is added to `FEED`, also add a matching `<item>` to `feed.xml` (see below) so the RSS feed stays in sync.

## RSS feed

`feed.xml` is a static RSS 2.0 file at the repo root, manually kept in sync with the `FEED` array — it is not auto-generated. Each `<item>` needs `title`, `link`, `guid isPermaLink="true"` (same as `link`), `pubDate` (RFC 822, first of the month per entry's `date`, e.g. `Mon, 01 Jun 2026 00:00:00 GMT`), `category` (the type's full name, e.g. "Conference talk"), and `description` (the entry's `desc`, with `" (where · city)"` appended). New items go at the top, right after `<lastBuildDate>`, which should also be bumped to the new newest entry's date.
