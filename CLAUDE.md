# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Personal speaker website for Lena Fuhrimann (cloudlena.ch), hosted on GitHub Pages. A single self-contained HTML file: `index.html`, hosted at the root.

## Architecture

`index.html` is a plain, self-contained HTML/CSS/JS file — no build step, no framework, no bundler. Open it directly in a browser.

- **HTML**: Static structure — topbar, hero, controls (filter tabs + search input), feed container, footer. All feed entries are static `<article class="item">` markup directly in `#list` — the page is fully readable and crawlable with JavaScript disabled.
- **CSS**: Inlined in `<style>`. TokyoNight-inspired dark palette. Fira Code loaded from Google Fonts. The scroll-in fade animation is scoped under a `.js` class (added by an early inline script in `<head>`) so items stay visible by default when JS doesn't run.
- **Data**: Lives directly in the static `<article class="item">` markup in `#list` — there is no JS array. See "Adding content" below for the per-entry template.
- **JS**: A `<script>` block at the bottom of the file progressively enhances the static list — filter tabs, live search (matches against each item's visible text), IntersectionObserver for scroll-in animations, expand-on-click/hover/focus for the description reveal, keyboard shortcuts (`/` focuses search, `Esc` clears). None of it is required to see or read the feed content.

## Design tokens

Dark palette: `#1a1b26` background, `#c0caf5` primary text, `#bb9af7` accent purple, `#9ece6a` green. Display font is Fira Code (monospace); terminal-style `lena_` branding.

## Local preview

Open `index.html` directly in a browser — no server needed.

## Deployment

GitHub Pages serves the file directly from `index.html` at the root URL.

## Adding content

Add a new `<article class="item">` block directly inside `<div class="list" id="list">` in `index.html`. Entries are listed in plain document order with no sorting — insert the new block at the position matching its date, don't just append, and keep the list in date-descending order.

Template (fill in the placeholders, drop the poster `<span class="play">` if the URL isn't a `youtube.com` link):

```html
<article class="item" data-type="TYPE" aria-expanded="false">
  <div class="marker" aria-hidden="true"></div>
  <div class="poster r169" aria-hidden="true">
    <img src="images/THUMB.webp" alt="" loading="lazy" />
    <div class="shade"></div>
    <span class="play">▶</span>
    <!-- ▶ for talk, ♪ for podcast; omit <span class="play"> entirely for post/oss or non-YouTube links -->
  </div>
  <div class="body">
    <div class="metarow">
      <span class="badge">LABEL</span>
      <span class="date">Mon YYYY</span>
      <span class="sep">·</span>
      <span class="meta">META</span>
    </div>
    <h2><a href="URL" target="_blank" rel="noopener noreferrer">TITLE</a><span class="arr" aria-hidden="true">↗</span></h2>
    <div class="where">@ WHERE · CITY</div>
    <div class="reveal" aria-hidden="true"><div>
      <p class="desc">DESC</p>
      <div class="actions"><a class="btn sm" href="URL" target="_blank" rel="noopener noreferrer" tabindex="-1">ACTION ↗</a></div>
    </div></div>
  </div>
</article>
```

- `TYPE` / `LABEL` (same value, lowercase): `talk`, `podcast`, `post`, or `oss`.
- `THUMB`: a locally downloaded thumbnail in `images/` (e.g. `yt-<videoId>` for YouTube). If there's no thumbnail, drop the `<img>`/`<div class="shade">` and instead put `<div class="grad"></div><span class="glyph">GLYPH</span><span class="ven">WHERE · CITY</span>` inside `.poster` — glyphs: `▶` talk, `🎙` podcast, `✎` post, `❮❯` oss.
- `ACTION`: for `youtube.com` URLs use "▶&nbsp; Watch on YouTube" (talk) or "Listen / watch" (podcast); otherwise "View talk" (talk), "View episode" (podcast), "Read article" (post), "View on GitHub" (oss).
- The `.marker` div stays empty — its number is generated automatically by a CSS counter, so entries never need renumbering.
- The tab counts (`<span class="ct" id="ct-all">`, `id="ct-talk"`, etc. in the controls bar) are static numbers, not computed by JS — bump the `all` count and the relevant type's count by 1 when adding an entry.
- Escape `&`, `<`, `>`, `"` in any field that lands in an attribute or text (e.g. `&` → `&amp;`).

Whenever an entry is added, also add a matching `<item>` to `feed.xml` (see below) so the RSS feed stays in sync.

## RSS feed

`feed.xml` is a static RSS 2.0 file at the repo root, manually kept in sync with the `FEED` array — it is not auto-generated. Each `<item>` needs `title`, `link`, `guid isPermaLink="true"` (same as `link`), `pubDate` (RFC 822, first of the month per entry's `date`, e.g. `Mon, 01 Jun 2026 00:00:00 GMT`), `category` (the type's full name, e.g. "Conference talk"), and `description` (the entry's `desc`, with `" (where · city)"` appended). New items go at the top, right after `<lastBuildDate>`, which should also be bumped to the new newest entry's date.
