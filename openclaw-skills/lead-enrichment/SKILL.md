---
name: lead-enrichment
slug: lead-enrichment
description: Enrich lead and company data in Twenty CRM. Finds missing emails, LinkedIn profiles, company size, tech stack, funding stage, and calculates an ICP score. Use when you need to fill data gaps on a contact or company before outreach. Delegates research to Scout and writes results back to Twenty.
metadata: {"clawdbot":{"emoji":"🔬","os":["linux","darwin","win32"]}}
---

# Lead Enrichment

Enrich contacts and companies in Twenty CRM with missing data, then score leads against the Modology ICP.

---

## ICP (Ideal Customer Profile) — Modology Studios

Use this to score leads 0–100 before enrichment and update the `icpScore` field on the company record.

### KerfOs — Cabinet Design Software
**Best fit:**
- Role: Cabinet maker, woodworker, millwork shop owner, furniture manufacturer
- Company type: Small shop (1-20 employees), custom carpentry business
- Geography: US, Canada, UK, Australia
- Pain: Manual estimation taking hours; error-prone cut lists; no digital design tool

**Scoring:**
| Signal | Points |
|--------|--------|
| Job title contains "cabinet", "woodwork", "millwork", "carpenter", "furniture" | +35 |
| Company employs 1–20 people | +20 |
| Company is in manufacturing/trades sector | +15 |
| US/CA/UK/AU location | +10 |
| Has website (established business) | +10 |
| LinkedIn active in past 90 days | +10 |
| **Max:** | 100 |

### Gaphunter — Market Gap Discovery
**Best fit:**
- Role: Founder, product manager, startup operator, VC analyst
- Company type: Startup (pre-seed to Series A), accelerator alumni, VC portfolio
- Pain: Identifying underserved niches before building; competitive intelligence

**Scoring:**
| Signal | Points |
|--------|--------|
| Title contains "founder", "CEO", "CPO", "product manager", "GM" | +30 |
| Company stage: pre-seed, seed, or Series A | +25 |
| Active on LinkedIn/Twitter (startup content) | +20 |
| Has product shipped (not just idea stage) | +15 |
| US/EU/CA market | +10 |
| **Max:** | 100 |

### ClawCost — AI Cost Intelligence
**Best fit:**
- Role: Engineering manager, CTO, DevOps lead, FinOps practitioner
- Company type: Mid-stage startup (Series A–B), tech team 10–200 people
- Pain: Unpredictable AI/cloud costs; no visibility into model spend; LLM cost optimization

**Scoring:**
| Signal | Points |
|--------|--------|
| Title contains "CTO", "engineering", "DevOps", "infrastructure", "FinOps" | +30 |
| Company uses AI/LLM tools (mentioned in job posts, tech stack, LinkedIn) | +30 |
| Company stage: Series A or Series B | +20 |
| Engineering team > 5 people | +10 |
| US/EU market | +10 |
| **Max:** | 100 |

---

## Enrichment Workflow

### Step 1 — Identify Data Gaps

Query Twenty for leads missing key enrichment fields:

```graphql
{
  people(first: 50) {
    edges {
      node {
        id
        name { firstName lastName }
        emails { primaryEmail }
        linkedinLink { primaryLinkUrl }
        jobTitle
        company {
          id name
          employees
          techStack
          icpScore
          enrichedAt
          domainName { primaryLinkUrl }
          linkedinLink { primaryLinkUrl }
        }
      }
    }
  }
}
```

**Missing email**: `emails.primaryEmail` is null or empty
**Missing LinkedIn**: `linkedinLink.primaryLinkUrl` is null or empty
**Needs enrichment**: `company.icpScore` is null OR `company.enrichedAt` is more than 30 days ago

### Step 2 — Research (delegate to Scout)

For each lead needing enrichment, ask Scout:

```
Scout: Enrich this lead for outreach.

Name: [FIRST] [LAST]
Company: [COMPANY NAME]
Known URL: [DOMAIN if available]
Role: [JOB TITLE if available]

Find:
1. Their LinkedIn profile URL (search "[name] [company] site:linkedin.com")
2. Business email if not known (format: firstname@domain.com, or check website contact page)
3. Company size (number of employees — LinkedIn, Crunchbase, or website)
4. Funding stage (Bootstrapped/Pre-seed/Seed/Series A/Series B+ — check Crunchbase, LinkedIn)
5. Tech stack (check BuiltWith, job postings, or GitHub — list top 3-5 tools)
6. Recent news or notable events in last 90 days

Return structured data I can write back to the CRM.
```

For batch enrichment (10+ leads), use OpenFang lead hand:
```bash
curl -s -X POST http://127.0.0.1:4200/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"hand": "lead", "message": "Enrich these leads: LEADS_JSON. For each, find: linkedinUrl, email, companySize, fundingStage, techStack (top 3), recentNews (1 sentence)."}'
```

### Step 3 — Calculate ICP Score

After gathering data, calculate the ICP score based on the product tag on the opportunity.

Example for KerfOs lead:
- Title "Owner, Custom Cabinets" → +35
- 5 employees → +20
- Manufacturing sector → +15
- US location → +10
- Website found → +10
- LinkedIn active → +10
- **Total: 100/100**

### Step 4 — Write Back to Twenty

**Update person** (email, LinkedIn):
```graphql
mutation {
  updatePerson(id: "PERSON_ID", data: {
    emails: { primaryEmail: "found@email.com" }
    linkedinLink: { primaryLinkUrl: "https://linkedin.com/in/username" }
    jobTitle: "Owner"
    city: "Atlanta, GA"
  }) {
    id
    name { firstName lastName }
    emails { primaryEmail }
  }
}
```

**Update company** (enrichment fields):
```graphql
mutation {
  updateCompany(id: "COMPANY_ID", data: {
    employees: 8
    techStack: "QuickBooks, Excel, Shapr3D"
    icpScore: 85
    fundingStage: BOOTSTRAPPED
    enrichedAt: "2026-03-28T00:00:00Z"
  }) {
    id name icpScore
  }
}
```

**Add enrichment note** to person record:
```graphql
mutation {
  createNote(data: {
    title: "Enrichment — [DATE]"
    body: "Source: Scout web research\nLinkedIn: [URL]\nEmail confidence: [high/medium/low]\nICP Score: [N]/100\nKey signals: [WHY this score]\nTech: [STACK]\nFunding: [STAGE]\nNews: [RECENT EVENT]"
  }) { id }
}
```

Then attach:
```graphql
mutation {
  createNoteTarget(data: { noteId: "NOTE_ID", personId: "PERSON_ID" }) { id }
}
```

---

## Enrichment Quality Tiers

| Confidence | Criteria | Action |
|-----------|----------|--------|
| **High** | Email verified, LinkedIn confirmed, company data from official source | Write directly to CRM |
| **Medium** | Email guessed from domain pattern, LinkedIn found but not 100% match | Write with note flagging uncertainty |
| **Low** | Only name/company known, no contact info found | Write partial data, flag for manual review |

---

## Batch Enrichment Command

To enrich all NEW leads in Twenty (no ICP score yet):

```
1. Query all opportunities with outreachStatus = NEW_LEAD
2. For each, check if company.icpScore is null
3. Batch enrich in groups of 5 using Scout or OpenFang lead hand
4. Write results back
5. Sort by icpScore descending — highest score leads go first in outreach queue
6. Report: "Enriched N leads. Top prospects: [names + scores]"
```

---

## Priority Queue After Enrichment

After enriching, update the outreach priority:

```graphql
{
  opportunities(
    filter: { outreachStatus: { eq: NEW_LEAD } }
    orderBy: { company: { icpScore: DescNullsLast } }
    first: 10
  ) {
    edges {
      node {
        id name
        company { name icpScore }
        pointOfContact { name { firstName lastName } emails { primaryEmail } }
      }
    }
  }
}
```

High ICP score (80+) → run immediately
Medium ICP (50-79) → run in next batch
Low ICP (<50) → review manually before running
