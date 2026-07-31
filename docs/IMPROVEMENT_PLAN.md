# Portfolio Improvement Plan v4

**Owner:** Duke's Portfolio (tmduc2606.github.io)
**Date:** 2026-07-31
**Status:** Pending Approval

---

## Issues Analysis

### Issue 1: Project card headline feels off
**Screenshots:** `14-45-24.png`, `14-45-34.png` — card titles ("Thailand Domestic Tourism", "Vietnam Real Estate - Codename: Azeroth") appear as unstyled text above the image.

**Root cause:** In `content/projects/_index.md`, the `<h3>` title is a direct child of `.project-card`, rendered **above** the `<img>` — outside the `.project-info` div. The CSS rule `.project-info h3` only targets `<h3>` inside `.project-info`, so the actual title gets no styling. It renders as plain unstyled heading text floating above the image.

**Current structure (broken):**
```
.project-card
  ├── h3 (title)       ← bare, unstyled
  ├── img              ← image
  └── .project-info    ← links, date, description
```

**Fix options:**
1. **CSS-only:** Add `.project-card > h3` rule to style the title as an overlay inside the card (positioned at bottom over the image, like a card hero caption)
2. **Markdown restructure:** Move `<h3>` inside `.project-info` so it appears below the image, properly styled
3. **Hybrid:** Keep `<h3>` above image, style it as a card header band with background

**Recommended:** Option 1 — style the `<h3>` as an image overlay caption (bottom-left over the image), matching common portfolio card patterns. This keeps the visual hierarchy compact and modern.

### Issue 2: Enhance homepage banner quote
**Screenshot:** `14-49-55.png` — banner title "When all that lies ahead is struggle, choose the path of greatest resistance" is plain white text on gradient overlay, no visual quote treatment.

**Root cause:** The theme's `.post-head-wrapper .post-title` applies white color and gradient background, but no quote-specific styling (no quotation marks, no italic, no decorative elements).

**Fix:** Add CSS to style the homepage banner title as a blockquote:
- Opening/closing quotation marks via `::before`/`::after` pseudo-elements
- Italic font style
- Slightly increased letter-spacing for readability
- Optional: subtle text-shadow for depth

---

## Implementation Plan

### Phase 1: Fix project card headline

**1.1 Add CSS for `.project-card > h3`**
- File: `assets/css/custom.css`
- Style the `<h3>` as an overlay caption positioned at the bottom of the image:
  ```css
  .project-card > h3 {
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      padding: 2rem 1.25rem 1rem;
      margin: 0;
      font-size: 1.15rem;
      font-weight: 600;
      color: #fff;
      background: linear-gradient(to top, rgba(0,0,0,0.7) 0%, transparent 100%);
      z-index: 1;
  }
  ```
- Also update `.project-card` to add `position: relative` (needed for absolute positioning of `<h3>`)
- Add dark mode override: ensure text stays white in dark mode

### Phase 2: Enhance homepage banner quote

**2.1 Add quote styling to `.post-head-wrapper .post-title`**
- File: `assets/css/custom.css`
- Add decorative quote marks and italic styling:
  ```css
  .post-head-wrapper .post-title {
      font-style: italic;
      font-weight: 400;
      letter-spacing: 0.02em;
      text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
  }
  .post-head-wrapper .post-title::before {
      content: "\201C";   /* left double quote */
      margin-right: 0.3em;
      font-size: 1.2em;
      opacity: 0.6;
  }
  .post-head-wrapper .post-title::after {
      content: "\201D";   /* right double quote */
      margin-left: 0.3em;
      font-size: 1.2em;
      opacity: 0.6;
  }
  ```
- Add dark mode override if needed (theme already handles `.post-head-wrapper *` color)

---

## Files Summary

| File | Change |
|---|---|
| `assets/css/custom.css` | Add `.project-card > h3` overlay styles; add `.post-head-wrapper .post-title` quote styling |

---

## Verification Checklist

- [ ] Project card titles overlay the image at bottom (not plain text above)
- [ ] Project card titles readable in both light and dark modes
- [ ] Homepage banner shows quotation marks around the title
- [ ] Banner title has italic styling and text shadow
- [ ] No visual regressions on other pages
- [ ] Build passes with 0 errors
