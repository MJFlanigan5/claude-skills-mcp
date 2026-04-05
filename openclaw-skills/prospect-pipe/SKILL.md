---
name: prospect-pipe
slug: prospect-pipe
description: Full prospect pipeline — scrapes a company URL with Firecrawl, enriches with Exa company research, sends to OpenFang lead-hand for scoring and memory storage, then optionally syncs high-scoring leads to Twenty CRM. One command turns a URL into a qualified lead record.
metadata: {"clawdbot":{"emoji":"🔬","os":["linux","darwin","win32"]}}
---

# Prospect Pipe

End-to-end prospect qualification pipeline. One input (URL or company name) → structured lead record in OpenFang memory + Twenty CRM sync for high scorers.

## What it does

1. **Firecrawl** scrapes the company website → extracts homepage content
2. **Exa** `company_research_exa` enriches with financials, headcount, contacts, LinkedIn
3. **OpenFang** `lead-hand` synthesizes both, scores fit (0-100), stores in memory
4. **Twenty CRM** — if score ≥ 65, create/update contact automatically

## How to invoke

### From a URL
```
Use the prospect-pipe to qualify: https://gong.io
Context: we're pitching Nerve (AI workflow automation) to revenue ops teams
```

### From a company name
```
Run prospect-pipe on Notion — target VP Product for Nerve pitch
```

### Batch (multiple companies)
```
Run prospect-pipe on these companies and give me a ranked list:
- Salesforce (https://salesforce.com)
- HubSpot (https://hubspot.com)
- Pipedrive (https://pipedrive.com)
Context: pitching KerfOS AI assistant integrations to CRM platforms
```

## Step-by-step execution

When a user asks you to run the prospect-pipe, follow these steps **in order**:

### Step 1 — Firecrawl scrape
Use the `firecrawl` MCP server, tool `firecrawl_scrape`:
- `url`: the company homepage
- `formats`: ["markdown"]
- `onlyMainContent`: true

Extract from the result: company name, what they do, any product/pricing info, team size signals.

### Step 2 — Exa company research
Use the `exa` MCP server, tool `company_research_exa`:
- `companyName`: company name from step 1

Extract: employee count, revenue, founding year, headquarters, key contacts, LinkedIn URL, recent news.

### Step 3 — OpenFang lead-hand research
Use the `openfang` MCP server, tool `openfang_research_lead`:
- `company`: company name
- `context`: combine the Firecrawl content + Exa data + user's stated context

The lead-hand will score the fit, identify target contacts, draft outreach angle, and **store in memory**.

### Step 4 — Score check → Twenty CRM
If the lead-hand returns a score ≥ 65:
- Use the `twenty-crm` skill to create a Person record with:
  - Name, title from the identified contacts
  - Company name
  - LinkedIn URL (from Exa)
  - Note: paste the lead-hand's full research summary

## Output format

Always summarize results as:

```
## [Company Name] — Score: XX/100

**What they do**: ...
**Size**: ... employees, $...M revenue
**Target contact**: [Name, Title]
**Fit rationale**: ...
**Recommended opener**: ...
**CRM**: ✅ Created in Twenty / ⏭ Below threshold
```

## Notes

- For batch runs, process companies sequentially and output a ranked table at the end
- If Firecrawl returns an error (JS-heavy site, auth wall), fall back to Exa crawling only
- Always use the `openfang_research_lead` tool even if you already have Exa data — lead-hand stores the record in memory for future recall
- The pipe runs fine without a URL — company name alone works via Exa + lead-hand
