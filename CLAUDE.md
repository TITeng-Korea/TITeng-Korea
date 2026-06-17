# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running Locally

No build step required. Open `index.html` directly in a browser, or serve with:

```bash
npx http-server ./
```

## Architecture

This is a **no-framework, no-build static web project** for ㈜티아이티이엔지(TITeng Co., Ltd.) — a Korean fintech hardware company. All styling comes from Tailwind CSS CDN and Font Awesome CDN; there is no `package.json` or bundler.

### File layout

| Path | Purpose |
|---|---|
| `index.html` | Main Tech Blog & DevPortal SPA |
| `presentation.html` | Alternate presentation version of the main page |
| `product/01-nuvia.html` … `product/10-tcp40.html` | Standalone product detail pages (one per hardware product line) |
| `product-source/*.png` | Product images referenced by the product pages |

### index.html internals

All business logic lives in the `<script>` block at the bottom of `index.html`. Key sections (each marked with a numbered comment):

1. **Article DB** — hardcoded `articles` array (mock data). Each article has `id`, `category`, `tags`, `content` (raw HTML string), and metadata fields.
2. **SDK code snippets** — `sdkCodeSnippets` object with per-language (Android Kotlin, iOS Swift, Windows C#, Embedded C++) tPay NFC demo code.
3. **App state** — four module-level variables: `currentCategory`, `searchQuery`, `selectedTag`, `isDarkMode`.
4. **Init & event listeners** — `DOMContentLoaded` handler boots dark mode from `localStorage`, then calls `renderArticles()`, `renderTagCloud()`, and `switchPlaygroundLanguage('android')`.
5. **Filtering logic** — `setCategory()`, `handleSearch()`, `filterByTag()`, `resetFilters()` all mutate state then call `renderArticles()`.
6. **Renderers** — `renderArticles()` filters the `articles` array and injects card HTML into `#posts-container`; `renderTagCloud()` builds the sidebar tag cloud.
7. **Code playground** — `switchPlaygroundLanguage(lang)` renders syntax-highlighted snippets from `sdkCodeSnippets` into `#code-output` using a lightweight manual highlighter (`highlightSimpleCode`).
8. **Clipboard** — `copyToClipboard()` uses `navigator.clipboard` with an `execCommand` fallback for non-secure contexts.
9. **SDK download mock** — `downloadSDK()` and `downloadSDKFromArticle()` show a toast only (no real download).
10. **Article modal** — `openArticle(id)` / `closeArticle()` drive the full-screen article reader modal with auto-generated TOC from `<h3>`/`<h4>` headings.
11. **Secondary interactions** — like/share buttons, toast notification system (`showToast(msg, iconName)`).

### Product pages (`product/*.html`)

Each product page is self-contained. They share the same Tailwind CDN setup and a single `toggleTheme()` function inlined in a `<script>` tag at the bottom. Product images are referenced as `../product-source/<filename>.png`. The navigation links back to `../index.html`.

## Content conventions

- **Article content** is raw HTML stored as a template literal in the `articles` array inside `index.html`. Math/formula strings use `$...$` notation (rendered as plain text — there is no MathJax).
- **Product images** filenames use lowercase English, numbers, and hyphens (e.g., `nuvia-hero.jpg`, `tcp40-interface.png`).
- The UI language is Korean (`lang="ko"`). All copy, button labels, and toast messages are in Korean.
- Dark mode is toggled via `document.documentElement.classList.toggle('dark')` and persisted to `localStorage` under the key `'theme'`.
