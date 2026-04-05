---
name: design-review
description: Elite web design review skill. Audit any URL or component against the 7-level Design Skills Masterplan. Covers accessibility, performance, visual design, animations, and Awwwards-level polish.
author: Felix (Modology Studios)
version: 1.0.0
keywords:
  - design
  - review
  - audit
  - accessibility
  - performance
  - awwwards
  - ui
  - ux
  - web-design
  - design-system
---

# Design Review Skill

Audit any webpage or component against the Modology Studios Design Skills Masterplan — the 7-level progression system from Foundation to Awwwards-level polish.

## Trigger Words

Use this skill when the user mentions:
- "design review"
- "design audit"
- "review this page"
- "check the design"
- "is this site good"
- "how does this look"
- "design feedback"
- "audit this component"
- "check accessibility"

## How It Works

### 1. Quick Mental Review (No Tools Needed)
For a fast opinion on screenshots or descriptions:

Rate against the 7 levels and give:
- **Level achieved** (1–7)
- **Top 3 issues** (prioritized by impact)
- **Quick wins** (easiest fixes with biggest payoff)
- **Anti-pattern violations** (anything from the no-fly list)

### 2. Automated URL Audit (Playwright)
For live site reviews, run the audit script:

```bash
# Full audit with screenshots
node ~/.openclaw/workspace/skills/design-review/scripts/run-review.mjs "https://example.com"

# Accessibility-only check
node ~/.openclaw/workspace/skills/design-review/scripts/run-review.mjs "https://example.com" --a11y

# Performance snapshot
node ~/.openclaw/workspace/skills/design-review/scripts/run-review.mjs "https://example.com" --perf
```

### 3. Component Review
When reviewing code snippets or components, check against:

**The Masterplan Checklist (every component must pass):**
- [ ] Responsive at 375px, 768px, 1024px, 1440px
- [ ] Keyboard navigable with visible focus
- [ ] Screen reader accessible (ARIA labels, roles)
- [ ] Smooth hover states (150-300ms transitions)
- [ ] `cursor: pointer` on clickable elements
- [ ] `prefers-reduced-motion` respected
- [ ] SVG icons (no emojis as icons)
- [ ] Light mode contrast 4.5:1 minimum
- [ ] No layout shifts (CLS < 0.1)
- [ ] Touch targets ≥ 44×44px on mobile

## Review Output Format

Always structure reviews as:

### 🎯 Score
**Level X/7** — [one-line summary]

### ✅ What's Working
- [specific things done well]

### 🚨 Issues (Priority Order)
1. **[CRITICAL]** — [issue affecting usability/accessibility]
2. **[HIGH]** — [issue affecting visual quality]
3. **[MEDIUM]** — [nice-to-fix improvements]
4. **[LOW]** — [polish opportunities]

### 🔧 Quick Wins
- [changes with highest impact/effort ratio]

### 🚫 Anti-patterns Detected
- [violations from the no-fly list]

## Design Token Reference

Always compare against the Modology Studios token system:

| Token | Value | Use |
|-------|-------|-----|
| Primary | `#6366f1` (Indigo) | Main brand, CTAs |
| Secondary | `#8b5cf6` (Violet) | Supporting |
| Accent | `#06b6d4` (Cyan) | Highlights |
| Background | `#09090b` | Page background |
| Surface | `#18181b` | Cards, modals |
| Text | `#fafafa` | Primary text |

**Typography:** 1.25 ratio scale, Inter family
**Spacing:** 8px grid (4px base unit)
**Radius:** Consistent scale, not random values
**Shadows:** sm/md/lg/xl/glow elevation levels
**Timing:** 150ms fast / 250ms normal / 400ms slow

## Anti-Patterns (Auto-flag These)

**Visual:**
- ❌ AI purple/pink gradients as default
- ❌ Emojis as icons (use Lucide/Heroicons SVG)
- ❌ Generic stock photos
- ❌ Border-radius inconsistency
- ❌ Low contrast text (< 4.5:1)

**Interaction:**
- ❌ No hover states on clickable elements
- ❌ Animations without `prefers-reduced-motion` fallback
- ❌ Hidden focus states
- ❌ Small touch targets (< 44×44px)

**Content:**
- ❌ Center-aligned body text > 2 lines
- ❌ All-caps body text
- ❌ Lorem ipsum in production
- ❌ Non-descriptive link text ("click here")

## References

- Masterplan: `/Users/mflanigan/.openclaw/workspace/design-skills-masterplan.md`
- Token CSS: `./tokens.css`
- Audit Script: `./scripts/run-review.mjs`
