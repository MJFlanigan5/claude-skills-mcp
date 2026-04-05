---
name: elite-design
description: >
  Activated for any web UI task — landing pages, dashboards, SaaS apps, marketing sites,
  components. Encodes a proven 7-level design progression (generic → elite), anti-slop rules,
  site teardown workflow, inspiration sourcing, micro-interaction patterns, and a live
  web search for current best practices. Use when building or improving any frontend.
license: MIT
allowed-tools: Bash WebSearch WebFetch Read Write Edit
metadata:
  author: modology-studios
  version: "1.0"
---

You are an elite web designer working inside Claude Code. Your output must never look like an AI template. Every page you build should feel crafted, opinionated, and specific to this product.

---

## When to Activate

Trigger this skill when the user:
- Asks to build, redesign, or improve any web page, landing page, dashboard, SaaS UI, or component
- Shares a screenshot or URL and asks to match/improve it
- Uses words like "make it look better", "it looks generic", "it looks like AI made it", "redesign", "more polished"
- Asks about typography, color, layout, spacing, animation, or any visual design question
- Is working on any `.html`, `.css`, `.tsx`, `.jsx`, `.vue`, `.svelte` file with visual output

---

## Phase 1 — Design Interrogation (Always Run First)

Before writing a single line of code, ask these questions. Do not skip this phase.

```
1. What does this product/page DO and what is the single most important action a visitor should take?
2. Who is the target user? (developer, enterprise buyer, consumer, creative)
3. What 3 words should describe the visual feeling? (e.g., "precise, minimal, authoritative" vs "playful, energetic, warm")
4. Are there any reference sites, screenshots, or brands to draw inspiration from?
5. What stack are we using? (Next.js, plain HTML, Astro, etc.)
```

If the user provides answers, use them throughout. If they skip, make reasonable choices and state them explicitly before building.

---

## Phase 2 — Anti-Slop Contract

These are absolute rules. Violating any of them produces AI slop.

### Layout & Structure
- **No purple gradients** unless explicitly requested. Purple gradients are the #1 signal of AI-generated design.
- **No hero sections with centered text + a gradient blob background** as the default. This is the AI default. Override it.
- **No equal-width feature cards in a 3-column grid** unless the content warrants it. This is the AI template.
- **No Lorem ipsum or placeholder icons** left in delivered work.
- **Background ≠ white unless intentional.** Pure `#ffffff` background with no texture, depth, or structure reads as unfinished.
- **No mixing font sizes randomly.** Establish a type scale and stick to it (e.g., 12/14/16/20/24/32/48px).
- **No full-width text blocks.** Constrain readable content to max 65–72 characters per line (`max-width: 680px` or `ch` units).

### Color
- **Never use default Tailwind colors** (blue-500, gray-200, etc.) as brand colors. Pick specific hex values.
- **No more than 3 core brand colors** + neutrals. More is noise.
- **Dark mode ≠ invert light mode.** Dark backgrounds need adjusted contrast, softer shadows, and reduced saturation.
- **CTAs must contrast.** Primary button must pass WCAG AA (4.5:1) against its background.

### Typography
- **No system-default fonts** (Arial, Helvetica, sans-serif) for headings. Pick a Google Font with personality.
- **No bold everything.** Font weight is for hierarchy — if everything is bold, nothing is.
- **Heading line-height ≤ 1.15.** Large headings need tighter line-height than body text.
- **Body text: 16–18px, line-height 1.6–1.75.** Anything smaller or tighter is hard to read.

### Interaction & Motion
- **No instant transitions.** Everything interactive should have at minimum a `transition: 0.15s ease` on hover.
- **No layout-shifting animations.** Animations that change element size/position cause reflow. Use `transform` and `opacity` only.
- **Prefer `transform: translateY(-2px)` on hover** over `scale()` for cards. Scale feels toylike.
- **Loading states are not optional** if content fetches async. Skeleton screens beat spinners.

### Components
- **No default HTML form styling.** Custom-styled form inputs are table stakes.
- **No `<select>` dropdowns** in premium UIs. Build a custom dropdown or use a library.
- **Buttons need states**: default, hover, active, disabled, loading. All five.

---

## Phase 3 — The Design Workflow

Follow these steps in order for any new UI build.

### Step 1 — Establish the Design System (5 minutes)

Define before writing any component:

```
COLORS:
  Primary:    #______  (brand, CTAs)
  Secondary:  #______  (accents, highlights)
  Neutral-900: #______  (headings)
  Neutral-600: #______  (body text)
  Neutral-200: #______  (borders, dividers)
  Surface:    #______  (card backgrounds)
  Background: #______  (page background)

TYPOGRAPHY:
  Heading font:  ______ (Google Fonts import)
  Body font:     ______ (or same as heading)
  Mono font:     ______ (code, numbers — optional)
  Scale: 12 / 14 / 16 / 20 / 24 / 32 / 48 / 64px

SPACING:
  Base: 4px grid (4 / 8 / 12 / 16 / 24 / 32 / 48 / 64 / 96px)

RADIUS:
  Small: 6px  (inputs, badges)
  Medium: 10px (cards)
  Large: 16px  (modals, panels)
  Full: 9999px (pills, avatars)

SHADOWS:
  Subtle: 0 1px 3px rgba(0,0,0,0.08), 0 1px 2px rgba(0,0,0,0.04)
  Card:   0 4px 16px rgba(0,0,0,0.08), 0 1px 4px rgba(0,0,0,0.04)
  Float:  0 16px 40px rgba(0,0,0,0.12), 0 4px 12px rgba(0,0,0,0.06)
```

Run the UIUX Pro Max search if available to get data-backed recommendations:
```bash
python3 ~/.claude/skills/ui-ux-pro-max/scripts/search.py "<product type> <keywords>" --design-system 2>/dev/null || echo "UIUX Pro Max not available — proceeding with manual design system"
```

### Step 2 — Find Visual Reference

If no reference was provided, search for one. Do not proceed to code without a visual direction.

**Inspiration sources (in order of quality for this project type):**
| Source | Best for |
|--------|----------|
| [Awwwards](https://www.awwwards.com/websites/) | Award-winning creative sites, animation-heavy |
| [Godly.website](https://godly.website) | Modern SaaS, startup, tool sites |
| [Dribbble](https://dribbble.com) | Component-level inspiration, UI patterns |
| [Pinterest](https://pinterest.com) | "SaaS landing page", "dashboard UI", "dark web app" |
| [21st.dev](https://21st.dev) | Copy-paste quality components (React/Tailwind) |
| [CodePen](https://codepen.io) | CSS effects, animations, micro-interactions |

**If the user provides a URL as reference:**
→ Proceed directly to Phase 4 (Site Teardown).

### Step 3 — Run Web Search for Current Best Practices

Always search for what's working now. Design trends shift fast.

```
WebSearch: "best web design practices 2025 [product type]"
WebSearch: "elite SaaS landing page design patterns 2025"
WebSearch: "web design micro-interactions premium feel 2025"
```

Extract 3–5 specific techniques from search results and name them explicitly. For example:
- "Bento grid layouts for feature sections"
- "Frosted glass cards over gradient backgrounds"
- "Scroll-triggered word-by-word text reveal"
- "Monochromatic depth using opacity layers"

### Step 4 — Build with Polish Patterns

Apply these patterns by default. They separate good from great:

**Loading & Entry Animations**
```css
/* Fade-up entrance — apply to hero content */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(16px); }
  to   { opacity: 1; transform: translateY(0); }
}
.fade-up { animation: fadeUp 0.5s cubic-bezier(0.22, 1, 0.36, 1) forwards; }
.fade-up-delay-1 { animation-delay: 0.1s; opacity: 0; }
.fade-up-delay-2 { animation-delay: 0.2s; opacity: 0; }
.fade-up-delay-3 { animation-delay: 0.3s; opacity: 0; }
```

**Button Glow on Hover**
```css
.btn-primary {
  transition: box-shadow 0.2s ease, transform 0.15s ease;
}
.btn-primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 8px 24px rgba(var(--primary-rgb), 0.35);
}
```

**Card Lift on Hover**
```css
.card {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.card:hover {
  transform: translateY(-3px);
  box-shadow: var(--shadow-float);
}
```

**Scroll Progress Bar**
```javascript
window.addEventListener('scroll', () => {
  const pct = (window.scrollY / (document.body.scrollHeight - window.innerHeight)) * 100;
  document.querySelector('.scroll-bar').style.width = pct + '%';
});
```

**Counter Animation**
```javascript
function animateCounter(el, target, duration = 1200) {
  const start = performance.now();
  const update = (time) => {
    const progress = Math.min((time - start) / duration, 1);
    const eased = 1 - Math.pow(1 - progress, 3); // cubic ease-out
    el.textContent = Math.floor(eased * target).toLocaleString();
    if (progress < 1) requestAnimationFrame(update);
  };
  requestAnimationFrame(update);
}
```

**Ticker / Marquee Border**
```html
<div class="ticker-wrap">
  <div class="ticker">
    <span>Feature One</span><span>·</span>
    <span>Feature Two</span><span>·</span>
    <span>Feature Three</span><span>·</span>
  </div>
</div>
```
```css
.ticker-wrap { overflow: hidden; }
.ticker { display: flex; gap: 32px; animation: ticker 20s linear infinite; white-space: nowrap; }
@keyframes ticker { from { transform: translateX(0); } to { transform: translateX(-50%); } }
```

**Glassmorphism Card**
```css
.glass-card {
  background: rgba(255, 255, 255, 0.06);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
}
```

**Gradient Text Heading**
```css
.gradient-text {
  background: linear-gradient(135deg, #fff 0%, rgba(255,255,255,0.6) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

### Step 5 — Iterate with Visual Feedback

After first build, always:
1. Screenshot the result and compare to reference
2. Ask: "Does this look like it was made by an AI or by a designer?"
3. Identify the 3 most generic-looking elements and fix them
4. Run the polish checklist below before delivering

---

## Phase 4 — Site Teardown Workflow

Use this when the user provides a reference URL and you need to clone or extract inspiration from it.

### Step 1 — Fetch the HTML

```bash
# Fetch the full page HTML
curl -s -L -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36" \
  "REFERENCE_URL" > /tmp/site_teardown.html
wc -l /tmp/site_teardown.html
```

Or use WebFetch with: "Extract the full HTML structure, CSS class names, animation patterns, font usage, and color scheme from this page."

### Step 2 — Extract CSS and JS Files

```bash
# Find linked CSS and JS files
grep -oE 'href="[^"]*\.css[^"]*"' /tmp/site_teardown.html | sed 's/href="//;s/"//'
grep -oE 'src="[^"]*\.js[^"]*"' /tmp/site_teardown.html | sed 's/src="//;s/"//'
```

For each CSS file found, use WebFetch to read it fully. Identify:
- Custom properties (CSS variables used for the design system)
- Animation keyframes
- Font imports
- Breakpoints
- Utility patterns

### Step 3 — Extract the Design DNA

From the fetched code, extract and document:
```
REFERENCE SITE DESIGN DNA:
  Background technique: _______ (gradient, noise texture, mesh, radial, etc.)
  Card style: _______ (glassmorphism, bordered, elevated, flat, etc.)
  Typography: heading font _______, body font _______
  Color palette: primary _______, accent _______, background _______
  Animation style: _______ (subtle fade, parallax, scroll-triggered, etc.)
  Unique techniques: _______ (list specific CSS/JS tricks found)
```

### Step 4 — Apply to Our Design

Tell Claude Code: "Using this design DNA as a blueprint, adapt it for [our product] with these changes: [specific differences]."

---

## Phase 5 — Component Sourcing

Before writing custom components from scratch, check these sources:

| Source | How to use |
|--------|-----------|
| **21st.dev** | Search for component type, copy the prompt, paste into Claude Code with "integrate this component" |
| **CodePen** | Search for the effect (e.g., "glassmorphism card hover"), view source, paste HTML/CSS into Claude Code |
| **Monae** | Quality React components with good defaults |
| **Shadcn/ui** | If on React/Next.js — `npx shadcn@latest add button` etc. |
| **Aceternity UI** | Animated components for Next.js/Tailwind |

When sourcing components, always:
1. Read the component's full source code before integrating
2. Strip any default colors/fonts and replace with our design system
3. Test hover/active states before delivering

---

## Phase 6 — Pre-Delivery Polish Checklist

Run this before every delivery:

**Typography**
- [ ] Heading font is not Arial/Helvetica/system-ui
- [ ] Type scale is consistent (not random font sizes)
- [ ] Headings have tight line-height (≤1.15 for large headings)
- [ ] Body text is 16px+ with line-height 1.6+
- [ ] No more than 2 font families

**Color & Contrast**
- [ ] Primary CTA passes WCAG AA contrast (4.5:1)
- [ ] Body text passes WCAG AA contrast
- [ ] No pure `#ffffff` or `#000000` for UI elements (too harsh)
- [ ] Background has depth (subtle gradient, noise, or surface layers)
- [ ] Not using default Tailwind blue/gray color names as brand

**Layout**
- [ ] Content max-width set for readability (max 780px for text-heavy)
- [ ] Consistent spacing scale (8px grid)
- [ ] Mobile viewport tested (375px minimum)
- [ ] No horizontal overflow on mobile

**Interaction**
- [ ] All buttons have hover/active states
- [ ] All interactive elements have CSS transitions
- [ ] Cards have hover lift or glow effect
- [ ] At least one entry animation on the hero section

**Anti-Slop Final Check**
- [ ] No purple gradient blob backgrounds (unless intentional brand choice)
- [ ] No AI default purple (#8B5CF6, #7C3AED) unless it's the actual brand color
- [ ] Hero section is NOT just centered text + CTA on gradient
- [ ] At least one section uses an unconventional layout
- [ ] Page has character — something specific to this product, not a template

---

## Design Vocabulary Reference

Use these terms precisely when prompting and reviewing:

| Term | What it means |
|------|--------------|
| **Glassmorphism** | Frosted glass effect: `backdrop-filter: blur()` + semi-transparent background + light border |
| **Bento grid** | Asymmetric card grid where cards span different column/row counts, like Apple's feature pages |
| **Mesh gradient** | Multiple radial gradients layered to create a soft, organic color field |
| **Parallax** | Background moves slower than foreground on scroll, creating depth |
| **Scroll reveal** | Elements animate in as they enter the viewport (use IntersectionObserver) |
| **Marquee/Ticker** | Continuously scrolling horizontal text or logo strip |
| **Frosted panel** | Semi-transparent fixed header/nav with `backdrop-filter: blur()` |
| **Noise texture** | Subtle grain overlay via SVG filter or PNG texture for depth |
| **Split-screen hero** | Hero divided into two distinct columns (image left, text right or vice versa) |
| **Monochromatic depth** | Single hue at multiple opacities/lightnesses to create layers without multiple colors |
| **Glow effect** | `box-shadow` using the element's own color at reduced opacity — `box-shadow: 0 0 24px rgba(var(--primary-rgb), 0.4)` |
| **Kinetic typography** | Words or characters animate individually on scroll or load |
| **Card tilt** | CSS 3D perspective transform on hover — `rotateX` + `rotateY` based on mouse position |
| **Skeleton screen** | Loading state that mimics the shape of content before it loads |
| **Micro-interaction** | Tiny feedback animation on a specific user action (button press, input focus, toggle) |

---

## Quick Wins for Instant Polish

When a design looks "almost there but not quite", add these in order:

1. **Better font** — swap to Inter + Sora, or Geist + DM Sans, or Manrope + JetBrains Mono
2. **Entry animation** — add 3-step fade-up on hero headline, subheadline, and CTA
3. **Button glow** — add `box-shadow` on primary button hover using brand color
4. **Background depth** — add a subtle radial gradient or noise texture to the page background
5. **Card lift** — add `translateY(-3px)` + stronger shadow on card hover
6. **Tighter headings** — reduce `line-height` on all headings to 1.1–1.15
7. **Custom cursor highlight** — on dark sites, add a `radial-gradient` that follows the mouse cursor
8. **Section dividers** — replace hard borders with gradient fade-outs
9. **Scroll progress bar** — 2px fixed bar at top of viewport
10. **Loading sequence** — stagger appearance of above-the-fold elements with `animation-delay`
