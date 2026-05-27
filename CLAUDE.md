# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A 14-chapter Chinese-language e-book — **《从 Hermes 学 Agent》** — teaching agent engineering by reading the [Hermes Agent](https://github.com/NousResearch/hermes-agent) source code, cross-referenced with 2022–2026 frontier research (ReAct, Voyager, MemGPT, MCP, SWE-Bench, etc.).

The book is **hand-written multi-file HTML** with a shared CSS — no build step, no static site generator. The Hermes source lives at `hermes-agent/` as a **git submodule** (pinned to a specific commit so the line numbers cited in the book stay valid).

This is a **prose project**, not a code project. There is no Python/JS/test/lint pipeline. Editing means modifying HTML/CSS directly.

## Layout

```
/                          repo root
├── tutorial/              the book (15 HTML pages + 1 stylesheet)
│   ├── index.html         home / TOC
│   ├── ch01..ch14-*.html  chapters
│   └── assets/style.css   single shared stylesheet
├── hermes-agent/          git submodule — Hermes source (read-only reference)
├── README.md              public-facing intro
└── CLAUDE.md              this file
```

## Viewing the Book Locally

Mermaid loads from a CDN as an ES module, so the browser refuses to render diagrams from a `file://` origin. Use a local HTTP server:

```bash
cd tutorial && python3 -m http.server 8000
# open http://localhost:8000
```

For light edits where you don't need diagrams to render, opening `tutorial/index.html` directly works.

## Conventions That Are Easy to Break

### 1. The sidebar is hand-duplicated across all 15 HTML files

Every page has `<nav class="sidebar">…</nav>` containing the full TOC. There is **no template engine**. When adding/renaming/reordering a chapter, you must update the sidebar in **all 15 files**. The `<li class="current">` marker is the only per-page difference.

### 2. Section numbering is manual

Sections use `<h2 class="section"><span class="secnum">X.Y</span>…</h2>`. When **inserting** a new section in the middle of a chapter, every subsequent `secnum` must be renumbered. Use `sed -i.bak` with explicit line addresses to keep this safe — see how `ch13-frontier.html` was renumbered after a mid-chapter insertion.

### 3. Mermaid theme lives in two places

Each HTML page has a `<script type="module">` block in `<head>` calling `mermaid.initialize({ theme: 'neutral', themeVariables: {…} })`. The color values inside `themeVariables` **mirror the CSS custom properties in `assets/style.css`**. If you change the palette in CSS, also update the mermaid init block in every page (the helper scripts in `_retheme.py` / `_recolor_diagrams.py` were used to do this in bulk previously; recreate them as needed and delete after use).

Hardcoded mermaid `classDef fill:#… stroke:#… color:#…` declarations inside `<pre class="mermaid">` blocks are *also* color-sensitive — recolor with care.

### 4. Code blocks use manual span-based syntax highlighting

There is no JS highlighter. Code is wrapped in `<pre><code>` with inline `<span class="k|s|c|f|n|num|deco|builtin">` tokens. The token classes are defined in `assets/style.css`:

| Class | Meaning |
|-------|---------|
| `.k`  | keyword |
| `.s`  | string  |
| `.c`  | comment (italic) |
| `.f`  | function name |
| `.n`  | class/type name |
| `.num`| number  |

When adding Python code from Hermes source, hand-add these spans for keywords and key identifiers — don't try to make it look like full syntax highlighting; partial coloring on important tokens is the established pattern.

### 5. Hermes citation format

References to Hermes source use this exact pattern:

```html
<pre><code><span class="filename">agent/conversation_loop.py:644-669</span>
… code …
</code></pre>
```

The `filename` span is styled differently from the code body. **Line numbers must match the pinned submodule commit** — if you change the citation, verify in `hermes-agent/` first.

### 6. Callouts and research cards have semantic classes

- `.callout` / `.callout.warn` / `.callout.good` / `.callout.tip` / `.callout.research` — each requires `<span class="label">LABEL</span>` as the first child.
- `.research-card` for paper citations — requires `ref-label` + `ref-title` + `ref-meta` children.
- Exercises use `.exercises` block with `<span class="difficulty d-easy|d-medium|d-hard">` tags.

Don't invent new classes — extend the palette in CSS first.

## The Most Likely Future Task: Adding / Updating Content

### Adding a new chapter

1. Copy the closest existing chapter as a template.
2. Update `<title>`, breadcrumb, `chapter-head` (num + h1 + subtitle + meta), and footer prev/next links.
3. Set `<li class="current">` on this chapter in the sidebar; **also add this chapter's `<li>` to the sidebar in all other 14+ files**.
4. Add an entry to `index.html`'s `.toc-list` under the appropriate `.toc-part`.
5. Update the `.chapter-foot` of adjacent chapters to point to the new file.

### Adding a section mid-chapter

Insert the new `<h2 class="section">` and renumber subsequent `secnum` values. The chapter `.chapter-head .meta` ("本章约 X 字 / 阅读 ~Y 分钟") should be updated to reflect the new length.

### Changing the color palette

`assets/style.css` `:root` defines `--bg`, `--bg-elev`, `--bg-code`, `--fg`, `--accent` (currently `#8b1538` wine red), `--link`, etc. Update there, then mirror into the `mermaid.initialize` block in every HTML. Also scan for hardcoded `fill:#…` inside `<pre class="mermaid">` blocks.

### Updating a Hermes citation

Always verify against the **submodule commit** (`cd hermes-agent && git log -1`). If the cited line range no longer matches, either update the cite or bump the submodule pin.

## Submodule Handling

The book pins `hermes-agent/` to a specific commit so cited line numbers don't rot. After cloning this repo, contributors need:

```bash
git submodule update --init --recursive
```

When updating the pin (intentionally tracking a new upstream commit):

```bash
cd hermes-agent
git fetch && git checkout <new-sha>
cd .. && git add hermes-agent && git commit -m "submodule: bump hermes-agent to <new-sha>"
```

Then audit any `filename` spans in the book — line numbers may have shifted.

## Language Convention

Body prose is **Simplified Chinese**. Code, file paths, identifiers, and CSS class names are **English**. Section titles are mixed (Chinese phrasing with English technical terms). CLAUDE.md, README.md, and inline `<!-- HTML comments -->` are English to match upstream convention.

## What Not to Do

- **Don't commit changes inside `hermes-agent/`** — it's a read-only submodule reference. Any modification there is local clutter at best, a fork at worst.
- **Don't add a build system** (Astro, Jekyll, Vite, etc.). The deliberate choice is hand-written HTML; introducing a generator would invalidate every page-by-page edit pattern documented above.
- **Don't change `:root` colors** without also updating mermaid blocks across all 15 files (see §3 above).
- **Don't fabricate citations.** Every research card in the book has a real paper / blog backing it. If you add a new card, include the actual arXiv ID / URL / date.
