---
name: outreach-engine
slug: outreach-engine
description: Full-cycle automated outreach engine. Discovers leads, researches prospects, drafts personalized emails and LinkedIn messages, sends them, logs all activity in Twenty CRM, and schedules follow-ups. Orchestrates Scout (research), Scribe (copy), Felix (orchestration), himalaya (email), shrinkedin-mcp (LinkedIn), and twenty-crm (CRM).
metadata: {"clawdbot":{"emoji":"🚀","os":["linux","darwin","win32"]}}
---

# Outreach Engine

Full-cycle automated outreach: discover → research → draft → send → log → follow-up.

Orchestrates the full agent stack: **Scout** (research) + **Scribe** (copy) + **Felix** (orchestration) + **himalaya** (email) + **shrinkedin-mcp** (LinkedIn) + **twenty-crm** (CRM logging).

---

## Agent Roles

| Agent | Role in Outreach |
|-------|-----------------|
| **Felix (main)** | Orchestrates sequences, monitors heartbeat for due follow-ups, sends Discord/Slack status |
| **Scout** | Prospect research — LinkedIn profiles, company intel, recent news, pain points |
| **Scribe** | Drafts personalized emails and LinkedIn messages |
| **Fixer** | Handles sending mechanics, retries, and edge cases |
| **OpenFang lead hand** | Parallel batch lead processing via `http://127.0.0.1:4200` |

---

## Full Outreach Cycle

### Phase 1 — Lead Intake

New leads enter Twenty CRM at stage `NEW`. They come from:
- Manual entry via Twenty UI
- LinkedIn lead generator skill exports
- Scout's web research
- OpenFang lead hand batch imports

**Query leads ready for first contact:**
```graphql
{
  people(first: 10) {
    edges {
      node {
        id
        name { firstName lastName }
        createdAt
        updatedAt
      }
    }
  }
}
```

**⚠️ SCHEMA CHANGE ALERT (March 30, 2026):**
Twenty CRM GraphQL schema has changed significantly. The following fields are no longer available in the current schema:
- `emails { primaryEmail }`
- `jobTitle`
- `company { name domainName { primaryLinkUrl } }`
- `linkedinLink { primaryLinkUrl }`
- `opportunities { edges { node { id name stage } } }`

**Workaround needed:** 
1. Use basic contact info (name only)
2. Manually enrich with external data sources
3. Update queries when new schema documentation is available
4. Consider using REST API if GraphQL schema is unstable

**Also check for overdue follow-up tasks:**
```graphql
{
  tasks(first: 10) {
    edges {
      node {
        id
        title
        body
        dueAt
        status
      }
    }
  }
}
```

**⚠️ SCHEMA CHANGE ALERT (March 30, 2026):**
The `taskTargets` field and nested `person`/`opportunity` connections are no longer available in the current schema.
Workaround: Use task title/body to identify associated contacts.

---

### Phase 2 — Prospect Research (Scout)

Before drafting, Scout builds a prospect brief. Delegate to Scout:

```
Scout: Research [PERSON NAME] at [COMPANY NAME].
Find:
1. Their LinkedIn profile summary and recent activity
2. Company overview: what they do, size, recent news, funding
3. Likely pain points relevant to [OUR OFFERING]
4. Any mutual connections or warm angles
5. Recent posts, articles, or achievements to reference

Return a 3-4 sentence prospect brief I can use to personalize outreach.
```

Scout uses: `expert-web-research`, `web-search-pro`, `linkedin-lead-generator` skills.

**For batch research** (5+ leads), delegate to OpenFang lead hand:
```bash
curl -s http://127.0.0.1:4200/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "hand": "lead",
    "message": "Research these leads and return prospect briefs: [JSON_ARRAY_OF_LEADS]"
  }'
```

---

### Phase 3 — Copy Generation (Scribe)

After research, delegate to Scribe for copy. Give Scribe:
- Prospect brief from Scout
- Our offering/value prop context
- Which sequence step this is (first touch, follow-up #1, follow-up #2, breakup)
- Channel (email or LinkedIn)

**Email copy request:**
```
Scribe: Write a cold outreach email for [NAME] at [COMPANY].

Context: [PROSPECT BRIEF FROM SCOUT]
Our value: [WHAT WE'RE OFFERING — e.g., "We help SaaS companies automate their sales outreach with AI agents"]
Tone: Direct, no fluff, peer-to-peer (not salesy)
Length: 4-6 sentences max
CTA: Ask for a 20-minute call

Do NOT use: "I hope this email finds you well", "synergy", "touching base", "circle back"
DO: Reference something specific about their work or company
```

**LinkedIn message request (300 char limit):**
```
Scribe: Write a LinkedIn connection message for [NAME].
Context: [PROSPECT BRIEF]
Keep under 300 characters. Reference one specific thing. End with a soft ask, not a pitch.
```

**Sequence templates by step:**
- **First touch**: Personal hook + value + soft CTA
- **Follow-up #1** (3-5 days): New angle or resource, shorter than first
- **Follow-up #2** (7-10 days): Different medium (switch email→LinkedIn or vice versa)
- **Breakup** (14+ days): "Closing your file" — often gets replies

---

### Phase 4 — Send

#### Email via Himalaya

```bash
himalaya message send -a modology "From: Modology Studios Assistant <assistant@modologystudios.com>
To: RECIPIENT_EMAIL
Subject: EMAIL_SUBJECT

EMAIL_BODY"
```

**Full example:**
```bash
himalaya message send -a modology "From: Modology Studios Assistant <assistant@modologystudios.com>
To: jane@acmecorp.com
Subject: Question about Acme's onboarding flow

Hi Jane,

Saw your post about reducing churn in the onboarding phase — we've been working on exactly that problem with a few SaaS teams.

I'd love to share what's been working. Worth a 20-min call this week?

– Michael
Modology Studios"
```

#### LinkedIn via shrinkedin-mcp

The `linkedin` MCP server (shrinkedin-mcp) is available to all agents. Use these tools:

**Send connection request with note:**
```
Tool: linkedin_connect
Parameters:
  profile_url: "https://linkedin.com/in/janesmith"
  message: "Hi Jane — saw your post on onboarding. Working on similar problems. Would love to connect."
```

**Send message to existing connection:**
```
Tool: linkedin_send_message
Parameters:
  profile_url: "https://linkedin.com/in/janesmith"
  message: "Hey Jane, following up on my email — did it land? Happy to share what we've been building."
```

**Note**: LinkedIn messages are capped at 300 chars for connection requests, 8000 chars for direct messages.

---

### Phase 5 — Log in Twenty CRM

After every send, immediately log in Twenty. Never skip this step.

**Create outreach note:**
```graphql
mutation {
  createNote(data: {
    title: "Outreach sent — [DATE]"
    body: "Channel: [email/linkedin]\nSubject: [SUBJECT]\nBody: [FULL MESSAGE TEXT]\nStatus: Sent"
  }) {
    id
    title
    body
  }
}
```

**⚠️ SCHEMA CHANGE ALERT (March 30, 2026):**
The `createNoteTarget` mutation and note-person/note-opportunity connections are no longer available in the current schema.
Workaround: Store contact information in the note body for manual association.

**Update opportunity stage** after first contact:
```graphql
mutation {
  updateOpportunity(id: "OPP_ID", data: { stage: "SCREENING" }) {
    id 
    stage
  }
}
```

**⚠️ SCHEMA CHANGE ALERT (March 30, 2026):**
Opportunity stage values may have changed. Verify available stages in current schema.

---

### Phase 6 — Schedule Follow-Up

Always create the next follow-up task immediately after sending. Never leave a contact without a scheduled next touch.

```graphql
mutation {
  createTask(data: {
    title: "Follow-up #1 — [NAME] at [COMPANY]"
    body: "First touch sent [DATE] via [email/linkedin]. Next: send follow-up with [ANGLE/RESOURCE]. Reference: [NOTE_ID]\nContact: [PERSON_NAME]"
    status: "TODO"
    dueAt: "FOLLOW_UP_DATE"
  }) {
    id 
    title 
    dueAt
    status
  }
}
```

**⚠️ SCHEMA CHANGE ALERT (March 30, 2026):**
The `createTaskTarget` mutation and task-person connections are no longer available in the current schema.
Workaround: Include contact information in task body for manual association.

**Follow-up schedule:**
| Touch | When | Channel |
|-------|------|---------|
| First touch | Day 0 | Email |
| Follow-up #1 | Day 3-5 | Email |
| Follow-up #2 | Day 8-12 | LinkedIn |
| Breakup | Day 18-21 | Email |
| Re-engage | 60+ days if no reply | Email, fresh angle |

---

## Orchestration Sequences

### Sequence A — Cold Email + LinkedIn (Standard B2B)

Felix orchestrates this sequence when a new `NEW` stage lead is found:

```
1. Felix queries Twenty for NEW leads
2. Felix delegates research to Scout (or OpenFang lead hand for batches)
3. Scout returns prospect briefs
4. Felix delegates email copy to Scribe
5. Fixer sends emails via himalaya
6. Felix logs notes in Twenty + moves to SCREENING
7. Felix creates Follow-up #1 tasks (due in 4 days)
8. Felix sends LinkedIn connection request via shrinkedin-mcp
9. Felix logs LinkedIn action as note in Twenty
10. Felix reports summary to Discord/Slack
```

### Sequence B — Warm Follow-Up (Task Due)

Felix runs this on every heartbeat (every 30 min) when overdue tasks are found:

```
1. Felix queries Twenty for TODO tasks with dueAt <= now
2. For each task: fetches person + opportunity + note history
3. Delegates follow-up copy to Scribe (includes prior message context)
4. Sends via himalaya or shrinkedin-mcp depending on sequence step
5. Logs activity in Twenty
6. Marks current task DONE
7. Creates next follow-up task (or marks opportunity LOST if breakup sent with no reply)
8. Notifies user via Discord if any high-priority deals moved
```

### Sequence C — Deal Progression (Stage Change Triggered)

When a deal moves to MEETING or PROPOSAL, Felix triggers:
```
1. Draft a meeting confirmation / prep email (Scribe)
2. Create task: "Prepare for meeting with [NAME]" due 24h before
3. After meeting: Create task "Send follow-up proposal" due same day
4. Log all context in Twenty
```

---

## Felix Heartbeat Duties (Every 30 Min)

Felix should check on every heartbeat cycle:

```
1. Query Twenty for overdue tasks (dueAt <= now, status TODO)
2. Query Twenty for NEW stage opportunities with no notes (never contacted)
3. If found: Run Sequence A or B as appropriate
4. If nothing urgent: Log "outreach check — clear" quietly (no notification)
5. If high-priority action taken: Post summary to Discord #outreach channel
```

**Felix's outreach check query:**
```graphql
{
  tasks(filter: { status: { eq: TODO }, dueAt: { lte: "NOW()" } }, first: 5) {
    edges { node { id title dueAt taskTargets { edges { node { person { name { firstName lastName } } } } } } }
  }
}
```

---

## Running the Outreach Status Check

From terminal, run:
```bash
~/.openclaw/scripts/outreach-status.sh
```

This shows:
- New leads ready for first contact
- Overdue follow-up tasks
- Active pipeline by stage
- Last 7 days outreach activity
- Recommended actions for today

---

## Batch Processing via OpenFang

For processing 10+ leads at once, delegate to OpenFang lead hand for parallel research:

```bash
curl -s -X POST http://127.0.0.1:4200/v1/chat \
  -H "Content-Type: application/json" \
  -d '{
    "hand": "lead",
    "message": "Process outreach batch. For each lead, research them and return: name, company, prospect_brief (3 sentences), recommended_hook, best_channel (email or linkedin). Leads: LEADS_JSON"
  }'
```

OpenFang returns structured briefs that Felix passes to Scribe for copy generation.

---

## Notifications

After completing any outreach sequence, Felix posts a summary:

**Discord format:**
```
🚀 Outreach Update — [DATE]

Sent today:
• [N] cold emails
• [N] LinkedIn connection requests
• [N] follow-up emails

Pipeline:
• NEW: [N] leads
• SCREENING: [N]
• MEETING: [N]
• PROPOSAL: [N]

Next follow-ups due: [DATE]
```

Post to Discord using the `discord` skill. Channel: `#outreach` or `#sales`.

---

## Quality Rules

1. **Never send without research** — always run Scout or OpenFang research first
2. **Never send twice** — check Twenty notes before sending; if a note exists for this channel+date combo, skip
3. **Always log** — every send gets a note in Twenty immediately after
4. **Always schedule next** — every send gets a follow-up task created
5. **Cap follow-ups** — max 4 touches per prospect per 30-day window
6. **Respect replies** — if a note indicates the prospect replied, do NOT continue sequence; escalate to Michael
7. **Rate limiting** — max 50 emails/day, max 20 LinkedIn messages/day via himalaya/shrinkedin

---

## Escalation to Michael

Flag to Michael (via Discord DM or #alerts channel) when:
- A prospect replies (positive or negative)
- A deal moves to MEETING stage
- A proposal is requested
- LinkedIn account shows any warning or restriction
- Daily send cap is reached
- Any send error occurs more than once
