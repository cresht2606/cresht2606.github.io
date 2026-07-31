# Portfolio Improvement Plan (Finalized)

**Owner:** Duke's Portfolio (tmduc2606.github.io)
**Date:** 2026-07-31
**Status:** Approved — Implemented

## Decisions (from user)

1. Projects Layout: **2-column grid**
2. Home Page: **Remove banner image entirely**
3. Contact Emojis: **FontAwesome 6.x (latest)**
4. Mobile Responsiveness: **Yes**
5. Color Scheme: **Keep current colors**
6. Must match the structural format of [hugo-theme-diary](https://github.com/amazingrise/hugo-theme-diary)
7. Broken Waline comments: fix per [Waline docs](https://github.com/walinejs/waline)

---

## Phase 1: Critical Bug Fixes

### 1.1 Navigation Highlighting
- **Root cause found:** theme's `sidebar.html` uses `hasPrefix $current (.URL|absLangURL)` — since Home's URL is `/`, every path starts with `/`, so Home was highlighted on *every* page (concurrent highlights with the real section).
- Fix: new `layouts/partials/sidebar.html` override — Home (`/`) must match exactly (`eq $current (absLangURL "/")`), other items keep `hasPrefix`.
- Also fixed the broken duplicate `class="a-block" class="nav-link-item"` attribute on the dropdown branch.

### 1.2 Waline Comment System (V3)
- **Root cause found:** Hugo 0.145+ HTML-escapes `{{ }}` output inside `<script>` blocks. The old template emitted `serverURL: "\"...\""` (literal backslashes in JS), producing an invalid server URL.
- Fix: `{{ ... | jsonify | safeJS }}` + Waline client V3 ES module (`import { init } from ...`).
- CSS: `https://unpkg.com/@waline/client@v3/dist/waline.css`
- Removed the theme's Waline **v2** script from `head.html` (new override) to avoid double-loading.
- Files: `layouts/partials/comment.html`, `layouts/partials/head.html` (theme copy minus Waline v2)

---

## Phase 2: Home Page

- Removed `featured_image` (banner) from `content/_index.md`.
- `title` → the tagline "When all that lies ahead is struggle, choose the path of greatest resistance" (rendered as the main-content header).
- `config.toml` `subtitle` → professional tagline: "Computer Science Undergraduate | Aspiring Data Engineer | Data Pipelines, ETL & Analytics Engineering | PySpark, Airflow, dbt, SQL | Data Science & AI" (sidebar only).
- `layouts/index.html`: removed the post list from Home (posts live in the Posts section only).

---

## Phase 3: Projects Section (2-Column Grid)

- Wrap each project in `.projects-grid` (2 columns, responsive to 1 column on mobile).
- Each project = `.project-card` with image + info (link, date, description).
- Files: `content/projects/_index.md`, `assets/css/custom.css`

---

## Phase 4: Contact Section

- FontAwesome 6.7.2 (latest 6.x) icons: `fa-solid fa-envelope`, `fa-brands fa-facebook`, `fa-brands fa-linkedin`, `fa-brands fa-github`, `fa-brands fa-kaggle`.
- Emojis in section headings (📬 🔗).
- Fixed broken Kaggle URL (`https://www.kaggle/` → `https://www.kaggle.com/cresht2606`).
- Files: `content/contact/_index.md`, `layouts/partials/extended_head.html`

---

## Phase 5: Custom CSS

- `assets/css/custom.css` loaded via Hugo Pipes from `layouts/partials/extended_head.html` (the theme's `head.html` ends with `{{- partial "extended_head.html" . }}` — the correct hook; also hosts the FontAwesome CDN link).
- Contents:
  - `.projects-grid` (2-column CSS Grid, collapses to 1 column ≤768px) + `.project-card`
  - Contact icon styling (`.contact-icon`, `.contact-item`)
  - Avatar sizing (150px circular)
  - Posts year-heading fix (`.year` forced to text color)
  - Nav active-state polish (`.nav-menu` — complementary to the sidebar fix)

---

## Phase 6: Posts Section Glitch Text

- Year heading `.year` forced to text color (not blue accent), bold, larger.

---

## Files Summary

**New:**
| File | Purpose |
|---|---|
| `assets/css/custom.css` | Custom styling overrides |
| `layouts/partials/extended_head.html` | FontAwesome CDN + custom CSS inclusion |
| `layouts/partials/sidebar.html` | Nav highlighting fix (Home exact-match) |
| `layouts/partials/head.html` | Theme copy minus Waline v2 (avoids v2/v3 conflict) |
| `docs/IMPROVEMENT_PLAN.md` | This tracking document |

**Modified:**
| File | Purpose |
|---|---|
| `layouts/partials/comment.html` | Waline V3 + safeJS fix |
| `layouts/index.html` | Removed post list from Home |
| `config.toml` | New sidebar subtitle (professional tagline) |
| `content/_index.md` | Remove banner, title = tagline |
| `content/projects/_index.md` | 2-column grid |
| `content/contact/_index.md` | FontAwesome + emojis + URL fix |

---

## Testing Checklist

- [x] Navigation highlights only one item at a time
- [x] Waline comments load and function (valid JS output verified)
- [x] Home page displays without banner image / post list
- [x] No duplicate "Cresht Tran"/tagline in main content
- [x] Projects in 2-column grid (desktop), 1-column (mobile)
- [x] Contact shows FontAwesome icons + emojis
- [x] Posts year headings display correctly
- [x] All pages return 200 (local smoke test)

## Verification Log

- `hugo --gc --minify` builds with 0 errors (v0.145.0+extended).
- Rendered HTML greps confirmed: nav active-state, projects grid/cards/dates, Waline v3 init, FA 6.7.2, custom CSS fingerprint.
- Local server smoke test: `/`, `/academic/`, `/projects/`, `/posts/`, `/contact/`, `/posts/welcome/` → all 200.
