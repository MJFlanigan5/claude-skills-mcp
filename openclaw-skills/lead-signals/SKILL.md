---
name: lead-signals
description: Buying signal detection pipeline. Uses opencli-rs to monitor Twitter, Reddit, and LinkedIn for companies and individuals showing intent to buy or build self-hosted AI infrastructure. Feeds qualified signals directly to OpenFang's lead-hand agent via API for enrichment and CRM routing. Use when: prospecting for ClawHQ/OpenClaw customers, finding companies evaluating self-hosted AI, or running a lead generation session.
---

# Lead Signals Pipeline

Monitors public conversations for buying signals → enriches with researcher-hand → routes to lead-hand for CRM export.

## OpenFang Agents
- **lead-hand** (ID: `f8bbf23d-820b-5f08-b291-313cd88d2ffc`) — enriches and exports leads
- **researcher-hand** (ID: `52a6d4df-6eb0-5c55-a200-b984514886ab`) — deep research on prospects
- OpenFang API: `http://localhost:4200/api/agents/{id}/message`
- Lead export directory: `/Users/mflanigan/.openfang/data/lead-hand/exports/`

---

## Step 1: Capture Buying Signals

### Twitter — Intent signals
```bash
# People evaluating self-hosted AI
opencli-rs twitter search "self-hosted AI looking for" --limit 20 --format json
opencli-rs twitter search "building AI agent team" --limit 15 --format json
opencli-rs twitter search "docker AI setup" --limit 15 --format json
opencli-rs twitter search "open source Claude alternative" --limit 10 --format json

# Pain points = buying signals
opencli-rs twitter search "AI agent coordination problem" --limit 10 --format json
opencli-rs twitter search "multi-agent setup messy" --limit 10 --format json
opencli-rs twitter search "OpenAI API bill expensive" --limit 20 --format json
opencli-rs twitter search "AI infrastructure cost" --limit 15 --format json
```

### Reddit — Research intent
```bash
# People actively evaluating tools
opencli-rs reddit search --query "self-hosted AI agent" --format json --limit 20
opencli-rs reddit search --query "alternatives to ChatGPT Teams" --format json --limit 10
opencli-rs reddit search --query "best open source AI platform" --format json --limit 15

# Specific subreddits with buyer intent
opencli-rs reddit subreddit --name "selfhosted" --format json --limit 30
opencli-rs reddit subreddit --name "LocalLLaMA" --format json --limit 20
opencli-rs reddit subreddit --name "artificial" --format json --limit 20
```

### LinkedIn — Company signals (job postings = hiring for AI = buyer)
```bash
# Companies actively building AI teams = potential ClawHQ customers
opencli-rs linkedin search "AI agent engineer" --limit 20 --format json
opencli-rs linkedin search "LLM infrastructure engineer" --limit 20 --format json
opencli-rs linkedin search "AI automation developer" --limit 15 --format json
opencli-rs linkedin search "self-hosted AI platform" --location "United States" --limit 20 --format json
```

### HackerNews — "Who is hiring" and "Who wants to be hired"
```bash
opencli-rs hackernews search --query "AI agents self-hosted" --format json
opencli-rs hackernews jobs --format json --limit 20
```

---

## Step 2: Qualify and Enrich

Send raw signals to researcher-hand for qualification:

```bash
curl -s -X POST "http://localhost:4200/api/agents/52a6d4df-6eb0-5c55-a200-b984514886ab/message" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Analyze these social signals for ClawHQ lead qualification. For each signal, identify: (1) company/person name, (2) role/title if available, (3) pain point expressed, (4) buying intent score 1-10, (5) recommended outreach angle. ClawHQ is a self-hosted AI agent platform — ideal for CTOs/engineering leads at 10-200 person companies building AI infrastructure. Signals: [PASTE_SIGNALS_JSON]"
  }'
```

---

## Step 3: Route Qualified Leads to lead-hand

For leads scoring 6+ from researcher-hand analysis:

```bash
curl -s -X POST "http://localhost:4200/api/agents/f8bbf23d-820b-5f08-b291-313cd88d2ffc/message" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "New qualified lead from signal monitoring. Please enrich and add to pipeline: Name: [name], Company: [company], Source: Twitter/Reddit/LinkedIn, Pain Point: [pain point], Buying Intent: [score]/10, Contact/Profile: [url]. Outreach angle: [angle from researcher]. Export to lead-hand CSV when enriched."
  }'
```

Or write directly to the lead export directory:

```python
# Python snippet for batch lead export
import json, csv, datetime, os

leads = [
  {
    "name": "Company Name",
    "title": "CTO",
    "company": "ACME AI",
    "location": "United States",
    "linkedin_url": "https://linkedin.com/in/...",
    "email": "",
    "score": 80,
    "notes": "Signal: asked about self-hosted AI on Reddit | Pain: API costs"
  }
]

export_dir = os.path.expanduser("~/.openfang/data/lead-hand/exports")
os.makedirs(export_dir, exist_ok=True)
filename = f"signal_leads_{datetime.datetime.now().strftime('%Y%m%d_%H%M%S')}.csv"
filepath = os.path.join(export_dir, filename)

with open(filepath, 'w', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=leads[0].keys())
    writer.writeheader()
    writer.writerows(leads)

print(f"Exported {len(leads)} leads to {filepath}")
```

---

## Complete Automated Pipeline

Run this for a full lead generation session:

```bash
#!/bin/bash
# lead-signal-run.sh — complete signal capture session

echo "=== Lead Signal Capture $(date) ==="

# Capture signals
echo "Pulling Twitter signals..."
opencli-rs twitter search "self-hosted AI" --limit 20 --format json > /tmp/signals_twitter.json

echo "Pulling Reddit signals..."
opencli-rs reddit subreddit --name "selfhosted" --format json --limit 20 > /tmp/signals_reddit.json

echo "Pulling LinkedIn job signals..."
opencli-rs linkedin search "AI agent engineer" --limit 20 --format json > /tmp/signals_linkedin.json

# Send to researcher for qualification
SIGNALS=$(cat /tmp/signals_twitter.json /tmp/signals_reddit.json /tmp/signals_linkedin.json)
echo "Sending to researcher-hand for qualification..."

curl -s -X POST "http://localhost:4200/api/agents/52a6d4df-6eb0-5c55-a200-b984514886ab/message" \
  -H "Content-Type: application/json" \
  -d "{\"message\": \"Qualify these signals for ClawHQ leads. Score each 1-10 for buying intent. Output as JSON array with: name, company, source, pain_point, score, outreach_angle. Signals: $SIGNALS\"}" \
  | python3 -m json.tool

echo "=== Done. Check researcher-hand output above. ==="
echo "Route 6+ scored leads to lead-hand: curl -X POST http://localhost:4200/api/agents/f8bbf23d-820b-5f08-b291-313cd88d2ffc/message"
```

---

## Buying Signal Keywords Reference

| Signal Type | Keywords | Platform |
|-------------|----------|----------|
| Cost pain | "AI API bill", "token costs", "OpenAI expensive" | Twitter, Reddit |
| Build intent | "self-hosted AI", "docker AI", "local LLM setup" | Reddit, HN |
| Team pain | "AI agent coordination", "multi-agent chaos" | Twitter, Reddit |
| Hiring signal | "AI agent engineer", "LLM infrastructure" | LinkedIn |
| Evaluation | "alternatives to", "looking for AI platform" | Reddit, Twitter |
| Frustration | "Claude API rate limit", "GPT-4 too expensive" | Twitter |

---

## ICP (Ideal Customer Profile) for ClawHQ

Target: **CTOs and engineering leads at 10-200 person companies** who:
- Are building internal AI tooling
- Have multiple Claude/GPT API integrations already
- Are frustrated with cost, coordination, or reliability
- Value open source and data control
- Are running on Docker/self-hosted infrastructure

Lead score 8-10: Direct outreach immediately
Lead score 6-7: Add to nurture sequence
Lead score 4-5: Monitor/tag for later
