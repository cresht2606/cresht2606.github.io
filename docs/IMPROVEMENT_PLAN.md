# Portfolio Improvement Plan v2

**Owner:** Duke's Portfolio (tmduc2606.github.io)
**Date:** 2026-07-31
**Status:** Pending Approval

---

## Decisions (from user)

1. Homepage banner: **Revive with `/images/banner.jpg`**
2. Academic banner: **Hero header with `/images/mathematics.jpg`**
3. Year heading: **Subtle gray separator** (match reference site)
4. Accent color: **Keep #3498db blue**
5. Pagination: **paginate=2** (demonstrate pagination with 3 posts)
6. Format: **Match hugo-theme-diary structural format**

---

## Issues Identified

### 1. Posts Section Formatting (Poorly Aligned)
- **Root cause:** Current `section.html` wraps year/post items in an extra `<div class="post">` container (lines 4-41) that the theme's default doesn't have.
- This causes the banner/header area to render even when there's no `featured_image` on the section page, pushing content down and breaking alignment.
- Theme's default `section.html` goes directly from `post-list-container` → year groups → post items (no intermediate `.post` wrapper).

### 2. Year Heading "2026"
- **Root cause:** Current `.year` CSS forces `font-weight: bold; font-size: 1.25rem; letter-spacing: 0.02em;` — too prominent.
- Reference site uses subtle gray separator lines, not bold text.
- Dark mode: year heading gets black text via `.post-item-wrapper * { color: $dark-mode-text }` — low contrast on dark background.

### 3. Color Inconsistency (Light/Dark)
- Light mode: year heading uses blue accent — inconsistent with reference.
- Need: subtle gray in both modes.

### 4. Missing Banners
- `content/_index.md` has no `featured_image` — homepage banner removed.
- `content/academic/_index.md` has no `featured_image` — Academic banner missing.

### 5. Pagination
- Theme's `extrabar.html` has pagination in right sidebar, conditional on `.Page.IsNode` and `.Page.Paginator`.
- Only works when `paginator.TotalPages > 1` — with 3 posts and default paginate=10, no pagination visible.

---

## Implementation Plan

### Phase 1: Restore Banners

**1.1 Homepage Banner**
- File: `content/_index.md`
- Change: Add `featured_image: "/images/banner.jpg"` to frontmatter
- Result: Homepage renders with hero header (title "When all that lies ahead..." overlaid on banner image)

**1.2 Academic Banner**
- File: `content/academic/_index.md`
- Change: Add `featured_image: "/images/mathematics.jpg"` to frontmatter
- Result: Academic section renders with hero header (title "Academic" + subtitle "Ongoing Plans & Education" overlaid on banner image)

### Phase 2: Fix Posts Section Formatting

**2.1 Remove Extra Wrapper**
- File: `layouts/_default/section.html`
- Change: Remove `<div class="post">...</div>` wrapper (lines 4-41)
- Match theme's default structure:
  ```html
  {{ define "main" }}
      <div class="post-list-container post-list-container-shadow">
          {{ range .Paginator.Pages.GroupByDate "2006" }}
              <!-- year heading -->
              {{ range .Pages }}
                  <!-- post items -->
              {{ end }}
          {{ end }}
      </div>
  {{ end }}
  ```

### Phase 3: Fix Year Heading Style

**3.1 CSS Override**
- File: `assets/css/custom.css`
- Change: Replace `.year` block with subtle gray separator:
  ```css
  .year {
      color: #999 !important;
      font-weight: 400;
      font-size: 1rem;
      letter-spacing: 0.05em;
      border-bottom: 1px solid rgba(128, 128, 128, 0.2);
      padding-bottom: 0.5rem;
      margin: 1.5rem 0 0.5rem 0;
  }
  ```
- Also fix `.post-item-meta` and post item colors for dark mode consistency

### Phase 4: Enable Pagination

**4.1 Config Change**
- File: `config.toml`
- Change: Add `paginate = 2` under `[params]`
- Result: With 3 posts, paginator creates 2 pages (2 + 1), pagination arrows appear in right sidebar

### Phase 5: Color Consistency

**5.1 Dark Mode Fixes**
- File: `assets/css/custom.css`
- Add overrides for dark mode to ensure consistent colors:
  ```css
  body.night .year {
      color: #888 !important;
  }
  body.night .post-item-meta {
      color: #aaa !important;
  }
  ```

---

## Files Summary

**Modified:**
| File | Change |
|---|---|
| `content/_index.md` | Add `featured_image: "/images/banner.jpg"` |
| `content/academic/_index.md` | Add `featured_image: "/images/mathematics.jpg"` |
| `layouts/_default/section.html` | Remove extra `.post` wrapper; match theme default |
| `assets/css/custom.css` | Fix `.year` style; add dark mode color overrides |
| `config.toml` | Add `paginate = 2` |

---

## Verification Checklist

- [ ] Homepage shows banner hero with title overlaid
- [ ] Academic section shows banner hero with title/subtitle overlaid
- [ ] Posts section year heading is subtle gray separator
- [ ] Year heading consistent in light and dark modes
- [ ] Post items properly aligned and formatted
- [ ] Pagination arrows appear in right sidebar (Posts section)
- [ ] All pages return 200
- [ ] Build passes with 0 errors

---

## Reference Comparison

**Rise's Blog (reference):**
- Year heading: `2021` as subtle gray line separator
- Post items: Title + date, clean spacing
- Pagination: 1 / - / 2 arrows in right sidebar
- Banner: Hero header with title overlaid

**Current Duke's Portfolio (before):**
- Year heading: Bold black text (dark) / Blue text (light)
- Post items: Misaligned due to extra wrapper
- Pagination: Not visible (paginate=10, only 3 posts)
- Banner: Missing

**After implementation:**
- Should match reference site's structural format
