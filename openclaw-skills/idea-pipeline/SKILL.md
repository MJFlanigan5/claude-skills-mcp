---
name: idea-pipeline
slug: idea-pipeline
description: Full idea-to-execution pipeline for Modology Studios. Takes a raw idea and automatically runs validation research, competitive analysis, scoping, design brief, build tasking, and launch prep. Orchestrates researcher-hand, collector-hand, lead-hand, Codex, Pixel, Scout, and Scribe.
metadata: {"clawdbot":{"emoji":"💡","os":["linux","darwin","win32"]}}
---

# Idea Pipeline

When an idea comes in, this skill runs the full automation stack — from raw thought to shipped product.

## Trigger phrases

Any of these starts the pipeline:
- "Run idea pipeline on: ..."
- "New idea: ..."
- "I have an idea for ..."
- "Validate this idea: ..."

---

## Pipeline stages

### Stage 0 — Intake + Sync (immediate, < 1 min)

Capture the raw idea. Extract:
- **One-line description**: What is it?
- **Target user**: Who has the pain?
- **Pain hypothesis**: What problem does it solve?
- **Modology fit**: Which existing product does it extend, or is it new?

Immediately sync to all three systems in parallel:

**A. Notion — Product Ideas database**
Use `notion` MCP tool `API-post-page` with flat parameters (NOT wrapped in `body`):
- `parent`: `{"database_id":"32def9ba-cc3a-81ea-8b46-d025fdc76de1"}`
- `properties`:
  - `Name` (title): idea name
  - `Status` (select): `Ideating`
  - `Progress` (select): `Idea`
  - `Features` (rich_text): one-line description + target user + pain hypothesis
  - `Last Updated` (date): today's date in YYYY-MM-DD format
- **Save the returned Notion page `id`** — you'll update this page at each subsequent stage using `API-patch-page` with `page_id` param

**B. Obsidian vault**
Write a new note to `/Users/mflanigan/Documents/Claude/Claude/memory/projects/ideas/YYYY-MM-DD-[idea-slug].md`:
```markdown
# [Idea Name]
Date: YYYY-MM-DD
Status: Intake

## Raw idea
[exact wording from intake]

## Initial assessment
- Target user: ...
- Pain hypothesis: ...
- Modology fit: ...
```
Create the `ideas/` directory if it doesn't exist.

**C. Twenty CRM**
Create a Note on the Modology Studios company record summarizing the intake.

---

### Stage 1 — Research (5-10 min)

Run in parallel — dispatch both simultaneously:

**A. researcher-hand** via `openfang_research`:
- Query: `"[idea name] market size, competitors, user pain points 2025 2026"`
- Focus: competitor landscape, pricing, user complaints, unmet needs
- Look for: Is anyone already doing this? What's missing?

**B. collector-hand** via `openfang_monitor`:
- Target: top 2-3 competitor names found in (A)
- Reason: competitive intelligence for [idea name]

**C. Exa `company_research_exa`** on top competitor:
- Get funding, headcount, pricing, recent moves

Synthesize into a **validation brief**:
```
## [Idea Name] — Validation Brief

**Market signal**: [1-2 sentences on market size/trend]
**Top competitors**: [list with key differentiator gaps]
**User pain (unmet)**: [what existing tools are failing at]
**Modology angle**: [why we can win this specifically]
**Verdict**: ✅ Validated / ⚠️ Crowded / ❌ No signal
```

If verdict is ❌ — stop and report. Don't proceed.

**Sync after Stage 1:**
- **Notion**: Update the page using `API-patch-page` with `page_id` = saved ID from Stage 0:
  - `Viability Grade` (select): map verdict → `A (Very High Viable)` / `B (High Viable)` / `C (Medium Viable)` / `F (Not Viable)`
  - `Progress` (select): `Research`
  - `Materials Needed` (rich_text): top 3 competitors + key gaps
  - `Future Features` (rich_text): unmet user needs found in research
  - `Last Updated` (date): today
- **Obsidian**: Append to the idea note under `## Research` with the validation brief

---

### Stage 2 — Scoping (2-3 min)

Felix + Claude define the MVP. Must answer:

1. **What is the ONE thing it does** — single core value prop
2. **Who is customer 0** — specific person/company type, not "everyone"
3. **What does v0.1 look like** — 3 bullets max, no more
4. **Revenue path** — how does this make money (subscription, usage, one-time)
5. **Unfair advantage** — why Modology specifically (tech, speed, network)
6. **Time to first customer** — realistic estimate in weeks

Output as a **Product Brief** stored in Twenty CRM as a Note.

**Sync after Stage 2:**
- **Notion**: Update the page using `API-patch-page`:
  - `Progress` (select): `Planning`
  - `Features` (rich_text): MVP scope (3 bullets)
  - `Pricing Tier` (select): closest match from existing options
  - `MRR` (number): target MRR number if applicable
  - `Last Updated` (date): today
- **Obsidian**: Append `## Scope` section with the full Product Brief

---

### Stage 3 — Design Brief

Build a Stitch-ready design prompt:

```
App: [name]
Type: [web app / mobile / dashboard / extension]
Core screen: [the one most important screen]
Palette: dark, primary=#69daff, secondary=#69f6b8, accent=#ac8aff
Font: Manrope headlines, Inter body
Vibe: [1-3 words: "clean professional SaaS" / "playful consumer" / etc.]
Key UI elements: [list 3-5 specific components needed]
```

**Sync after Stage 3:**
- **Notion**: Append design brief as a child block using `API-post-block-children` with `block_id` = saved page ID and `children` = array of paragraph blocks containing the brief
- **Obsidian**: Append `## Design Brief` section

Until Stitch has a real API, Pixel agent uses this brief to:
- Generate UI wireframes via Excalidraw skill
- Draft component structure for Codex
- Create a design system extension in `globals.css` format

### Stage 3b — Landing page copy (runs in parallel with Stitch/Pixel)

Claude Code drafts a complete, production-ready landing page using the design brief and Stage 2 scope. Output as a single `landing-page.md` file saved to the idea notes directory:

```markdown
# [Product Name] — Landing Page

## Hero
**Headline**: [sharp, benefit-focused, ≤8 words]
**Subhead**: [1 sentence expanding the headline]
**CTA**: [action verb + outcome — e.g. "Start for free", "See it in action"]

## Problem section
[2-3 sentences: the pain this person feels today, without the product]

## How it works (3 steps)
1. [Step 1 — what the user does]
2. [Step 2]
3. [Step 3 — the payoff]

## Benefits (3 bullets)
- **[Benefit 1]**: [1-line expansion]
- **[Benefit 2]**: [1-line expansion]
- **[Benefit 3]**: [1-line expansion]

## Social proof placeholder
"[Placeholder testimonial in voice of Customer 0 persona]"
— [Name], [Title] at [Company type]

## Pricing
[Based on Stage 2 revenue model — 1-2 tiers max for v0.1]

## Footer CTA
[Repeat headline variant + CTA button]
```

Write to: `/Users/mflanigan/Documents/Claude/Claude/memory/projects/ideas/YYYY-MM-DD-[slug]-landing.md`

**Sync after Stage 3b:**
- **Notion**: Append landing page copy as child blocks on the idea page using `API-post-block-children`
- **Obsidian**: Write as separate file at path above

---

### Stage 4 — Build tasking

Create the GitHub repo and write a `CLAUDE.md` at the repo root — this is the handoff to Claude Code for actual implementation. When Felix or you open `claude` in that repo directory, Claude Code reads this file and begins executing immediately without needing further instruction.

`CLAUDE.md` format:
```markdown
# [Product Name] — Build Tasks

> Generated by idea pipeline on [date]. Open `claude` in this directory to begin.

## Context
- **What it is**: [one-line from Stage 2]
- **Customer 0**: [from Stage 2]
- **Revenue model**: [from Stage 2]
- **Design brief**: See `/docs/design-brief.md`
- **Landing page copy**: See `/docs/landing-page.md`

## Repo setup
- [ ] Next.js 16 + Tailwind v4 scaffold (copy pattern from revenue-dashboard)
- [ ] Deploy to Vercel, add to modologystudios.com subdomain
- [ ] Configure environment variables

## Core feature — v0.1
[3-5 specific implementation tasks based on Stage 2 scope, written as actionable engineering tasks]

## Integrations
- [ ] Twenty CRM — create product record, link to pipeline
- [ ] Stripe — basic checkout if monetized
- [ ] OpenClaw — add agent profile if AI-powered

## Launch checklist
- [ ] Landing page from `/docs/landing-page.md` implemented at `/`
- [ ] OG tags + meta description
- [ ] Basic analytics (Plausible or GA4)

## Done when
[1-sentence definition of "shipped v0.1" — the minimum to show Customer 0]
```

Also copy `landing-page.md` and `design-brief.md` from the idea notes directory into `/docs/` in the repo so Claude Code has full context in one place.

Codex (GLM-5) handles repo scaffolding and boilerplate tasks from the checklist. Claude Code (`claude` CLI, run in the repo directory) handles complex multi-file architecture, landing page implementation, and anything requiring deep reasoning.

**Sync after Stage 4:**
- **Notion**: Update via `API-patch-page`: `Progress` → `MVP Development`, append GitHub repo link to `Materials Needed`
- **Obsidian**: Append `## Build Tasks` section with the full task list

---

### Stage 5 — Lead targeting

Scout + prospect-pipe immediately finds the first 5 potential customers:

```
Use prospect-pipe on these [industry] companies for [product name]:
Context: [one-line pitch based on Stage 2 value prop]
```

Any leads scoring ≥65 go to Twenty CRM as contacts with opportunity stage = `NEW`.

---

### Stage 6 — Launch prep

Scribe drafts:
1. **Landing page copy** — headline, 3 benefit bullets, CTA
2. **Cold outreach sequence** — 3-step email cadence for the Stage 5 leads
3. **Discord/Slack announcement** — for #openclaw channel

Felix posts the launch brief to Discord.

---

## Full output summary

When pipeline completes, output:

```
## 💡 [Idea Name] — Pipeline Complete

**Verdict**: ✅ Validated
**Market**: [1 line]
**v0.1 scope**: [3 bullets]
**Revenue model**: [1 line]
**Customer 0**: [who + why]

**Status**:
- Research: ✅ Stored in researcher-hand memory
- Competitors monitored: [list]
- Design brief: ✅ Ready for Stitch/Pixel
- Landing page copy: ✅ Drafted (in repo `/docs/landing-page.md`)
- Repo: [github link] — CLAUDE.md ready, open `claude` in repo to build
- Leads found: [N] → [M] in Twenty CRM
- Outreach: ✅ Sequences drafted by Scribe

**Synced to**:
- 📓 Notion: [Product Ideas page URL]
- 🗒 Obsidian: `/memory/projects/ideas/YYYY-MM-DD-[slug].md`
- 📇 Twenty CRM: Note on Modology Studios
- 💬 Discord: #openclaw announcement posted

**Next action**: [single most important next step]
```

---

## Notes

- Run Stages 1A + 1B in parallel — don't wait sequentially
- Run Stage 3 (Stitch/Pixel) and Stage 3b (landing page copy) in parallel
- If idea is an extension of an existing product (KerfOS, Gaphunter, etc.), skip Stage 4 repo setup — just add a CLAUDE.md to the existing repo
- Always log everything to Twenty CRM — ideas that die are still valuable signal
- The design brief in Stage 3 is also the Stitch input — once Stitch API is live, this feeds directly
- If Felix receives the idea via Slack/Discord, he kicks off this pipeline automatically
- **Claude Code handoff**: `cd` into the new repo and run `claude` — CLAUDE.md is the brief. No further setup needed. Codex handles scaffolding, Claude Code handles everything requiring reasoning.
