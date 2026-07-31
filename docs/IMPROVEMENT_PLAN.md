# Portfolio Improvement Plan v3

**Owner:** Duke's Portfolio (tmduc2606.github.io)
**Date:** 2026-07-31
**Status:** Pending Approval

---

## Issues Analysis (from screenshots + code review)

### Bug 1: Lost "Contact" content
**Screenshot:** `Screenshot from 2026-07-31 14-06-39.png` — content area is completely empty.
**Root cause:** `section.html` only renders `.Content` inside the `{{ if $featured_image }}` block. Contact has no `featured_image`, so its body content is never rendered.
**Fix:** Always render `.Content` for section pages. The banner/header should be conditional on `featured_image`, but the content body must always show.

### Bug 2: Projects section — no pagination indicators
**Screenshot:** `14-06-16.png`, `14-06-23.png` — sidebar has only back-to-top + dark mode toggle, no page indicators.
**Root cause:** Projects is a single section page with inline content (no child pages/posts to paginate). This is actually correct behavior — pagination only appears when there are multiple pages. However, the `pagerSize = 2` globally affects all list pages including Projects.
**Fix:** This is a non-issue. Projects has no child pages, so no pagination is expected. If the user wants pagination for project cards, that would require converting projects to individual content files (out of scope).

### Bug 3: Redundant bar in "2026" year heading
**Screenshot:** `14-06-28.png` — visible horizontal line under "2026".
**Root cause:** CSS `.year { border-bottom: 1px solid rgba(128, 128, 128, 0.2); }` creates a visible separator line.
**Fix:** Remove `border-bottom` from `.year` class. The year should be plain text, not a separator.

### Bug 4: Posts pagination should be 10 per page
**Current:** `pagerSize = 2` — only 2 posts per page.
**Fix:** Change to `pagerSize = 10` for the Posts section.

### Bug 5: Dark mode — Projects card text invisible
**Screenshots:** `14-06-16.png`, `14-06-23.png` — card titles, dates, and descriptions are nearly invisible (white/light text on white card background).
**Root cause:** Theme's `dark-mode.scss` sets `body.night .post-body *` and `body.night .post-list-container .post-item-wrapper *` colors, but `.project-card` elements are custom HTML inside `.post-body` and don't inherit these rules properly. The cards use `background: var(--bg-color, #ffffff)` which stays white in dark mode.
**Fix:** Add dark mode overrides for `.project-card`:
  - Card background → dark mode color
  - Card text → light color
  - Card border → subtle dark border

### Enhancement: Add Tags section
**Reference:** `n-pham.github.io/blog/` shows a Tags page with pill-style tag buttons.
**Fix:** Create `content/tags/_index.md` and add "Tags" to the menu. The theme already has taxonomy support (`[taxonomies] tag = "tags"`).

---

## Implementation Plan

### Phase 1: Fix Critical Bugs

**1.1 Fix Contact content not rendering**
- File: `layouts/_default/section.html`
- Change: Move `.Content` rendering outside the `{{ if $featured_image }}` block
- Structure:
  ```
  {{ if $featured_image }}
    <!-- banner header -->
  {{ end }}
  {{ .Content }}   ← always render
  {{ range .Paginator.Pages... }}
    <!-- post items -->
  {{ end }}
  ```

**1.2 Fix year heading redundant bar**
- File: `assets/css/custom.css`
- Change: Remove `border-bottom` from `.year` class
- Before: `border-bottom: 1px solid rgba(128, 128, 128, 0.2);`
- After: Remove the line entirely

**1.3 Fix Posts pagination to 10 per page**
- File: `config.toml`
- Change: `pagerSize = 2` → `pagerSize = 10`

### Phase 2: Fix Dark Mode

**2.1 Projects card dark mode**
- File: `assets/css/custom.css`
- Add dark mode overrides:
  ```css
  body.night .project-card {
      background: #282828;
      border-color: rgba(255, 255, 255, 0.1);
  }
  body.night .project-info h3,
  body.night .project-info p,
  body.night .project-date {
      color: #e0e0e0;
  }
  body.night .project-info a {
      color: #5dade2;
  }
  ```

**2.2 General dark mode text fixes**
- Ensure `.post-body` content in sections renders with correct dark mode colors

### Phase 3: Add Tags Section

**3.1 Create Tags page**
- File: `content/tags/_index.md`
- Content: Frontmatter only (title: "Tags"), Hugo auto-generates taxonomy list

**3.2 Add to navigation**
- File: `config.toml`
- Add menu entry:
  ```toml
  [[menu.main]]
  url = "/tags/"
  name = "Tags"
  weight = 6
  ```

**3.3 Style Tags page**
- File: `assets/css/custom.css`
- Add tag pill styles matching reference site (rounded pills with count)

---

## Files Summary

| File | Change |
|---|---|
| `layouts/_default/section.html` | Always render `.Content`, not just with `featured_image` |
| `assets/css/custom.css` | Remove `.year` border-bottom; add dark mode overrides for project cards; add tag styles |
| `config.toml` | Change `pagerSize` to 10; add Tags menu entry |
| `content/tags/_index.md` | **NEW** — Tags taxonomy page |

---

## Verification Checklist

- [ ] Contact page shows content (email, social links)
- [ ] Projects page shows content with banner
- [ ] Year heading "2026" has no horizontal bar
- [ ] Posts section shows 10 posts per page
- [ ] Projects cards readable in dark mode (text visible, card background dark)
- [ ] Tags page renders with all tags as pills
- [ ] Tags added to navigation menu
- [ ] Build passes with 0 errors
- [ ] All pages render correctly in both light and dark modes
