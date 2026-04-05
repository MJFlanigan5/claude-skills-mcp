---
name: board-meeting
slug: board-meeting
description: Compile a strategic board briefing from live business data, send it to the Board (Hermes at 192.168.2.41), and store the Board's recommendations. Run weekly (Sunday nightly deep dive) or any time Felix faces a significant decision.
metadata: {"clawdbot":{"emoji":"🏛️","os":["linux","darwin","win32"]}}
---

# Board Meeting — Strategic Advisor Integration

## Purpose
The Board of Directors is a Hermes agent running at `http://192.168.2.41`. It acts as an external strategic advisor with no operational bias — it challenges Felix's assumptions, identifies blind spots, and provides prioritized recommendations based on objective data.

The Board does NOT run operations. It reviews, questions, and directs. Felix executes.

---

## When to Run a Board Meeting
- **Weekly** — every Sunday during the nightly deep dive, automatically
- **Decision forks** — when Felix must choose between two product focuses, pricing strategies, or go-to-market approaches
- **Stall detection** — when pipeline hasn't moved in 2+ weeks
- **Milestone reviews** — after first paying customer, after hitting $1K MRR, at each $5K MRR step
- **Crisis mode** — if a product is getting zero traction after 30+ days of outreach

---

## Board Briefing Format

Always send a structured briefing. The Board needs facts, not summaries.

```
# Modology Studios — Board Briefing
Date: {YYYY-MM-DD}
Period: {week/sprint/milestone}

## Business Metrics
- MRR: ${current} (target: $30,000)
- Active pipeline leads: {count}
- Reply rate (last 7 days): {pct}%
- Qualified leads: {count}
- Products with active outreach: {list}

## What Happened This Week
- {bullet: concrete action taken}
- {bullet: concrete action taken}
- {bullet: what didn't happen and why}

## What's Working
- {specific, evidence-backed observation}

## What's Not Working
- {specific, evidence-backed observation}

## Key Decision(s) Pending
1. {decision}: {option A} vs {option B} — Felix's current lean: {lean}
2. {decision}: ...

## Metrics That Are Concerning
- {metric}: {value} — expected {expected}

## Felix's Proposed Next Week Plan
1. {priority action}
2. {priority action}
3. {priority action}

## Questions for the Board
1. {specific question Felix wants challenged}
2. {specific question}
```

---

## How to Send the Briefing

The Board API endpoint is at `http://192.168.2.41`. Send via the OpenClaw agent message trigger:

```bash
# Option 1: Via OpenClaw if Hermes is registered as an agent
openclaw agent --agent board --message "$(cat /tmp/board-briefing.md)"

# Option 2: Direct HTTP POST to Hermes API (if running standalone)
curl -s http://192.168.2.41/api/chat \
  -X POST \
  -H "Content-Type: application/json" \
  -d "{\"message\": $(cat /tmp/board-briefing.md | python3 -c 'import sys,json; print(json.dumps(sys.stdin.read()))'), \"context\": \"board-meeting\"}"

# Option 3: Via Paperclip wake endpoint (if Hermes is registered as Paperclip agent)
curl -s http://localhost:3101/api/agents/{BOARD_AGENT_ID}/wake \
  -X POST \
  -H "Content-Type: application/json" \
  -d "{\"message\": $(cat /tmp/board-briefing.md | python3 -c 'import sys,json; print(json.dumps(sys.stdin.read()))')}"
```

---

## Expected Board Response Format

Prompt the Board to respond in this structure:

```
# Board Response — {date}

## Overall Assessment
{1-2 sentence read on company health and trajectory}

## What You're Getting Right
- {specific positive with evidence}
- {specific positive}

## What Concerns the Board
- {concern}: {why it matters, what it signals}
- {concern}: ...

## Strategic Recommendations (ranked by urgency)
1. **[URGENT]** {recommendation} — {one-sentence rationale}
2. **[THIS WEEK]** {recommendation} — {rationale}
3. **[THIS MONTH]** {recommendation} — {rationale}

## Decision Guidance
{For each pending decision: clear recommendation with rationale}

## The Question Felix Isn't Asking
{The thing the board sees that Felix isn't focused on}

## Next Board Check-in
{What the board wants to see reported next week}
```

---

## Storing Board Feedback

After receiving a response, store it:

1. Write to `~/life/board/YYYY-MM-DD.md`
2. Extract action items and create tasks in Twenty CRM for any URGENT recommendations
3. Log a note in Felix's daily notes under `## Board Guidance`
4. If the Board recommends a strategic pivot, flag it to Michael immediately via Discord

```bash
# Store board response
mkdir -p ~/life/board
cat > ~/life/board/$(date +%Y-%m-%d).md << 'EOF'
{board response content}
EOF
```

---

## Board System Prompt

When registering the Board agent (Hermes at 192.168.2.41), use this as the system context:

```
You are the Board of Directors for Modology Studios, an early-stage AI software company founded by Michael Flanigan. The company is pre-revenue and targeting $30,000 MRR by end of 2026.

Products in development:
- KerfOS: AI cabinet design & cut lists for woodworkers
- Gaphunter: B2B review intelligence platform
- ClawCost: AI token cost tracking for engineering teams
- RenovateAI: AI-powered renovation planning for contractors
- ShopFlow: AI shop layout optimization for woodworkers
- Legal Compliance Platform: Compliance platform for SMBs

Your role is to:
1. Review weekly briefings from Felix (the CEO AI agent) and provide honest, direct strategic feedback
2. Challenge assumptions and identify risks Felix may be optimizing around
3. Give prioritized recommendations — ranked by what will most directly move toward $30K MRR
4. Ask the question Felix isn't asking
5. Hold Felix accountable to metrics and commitments from prior weeks

You are NOT a cheerleader. You push back when the plan is unfocused. You flag when 6 products means 0 products are getting real attention. You insist on evidence before validating a direction.

Respond in structured markdown. Be direct. Prioritize ruthlessly.
```

---

## Integration with Weekly Pipeline Report

The weekly-pipeline-report.sh script should call this skill after sending the email:
1. Pull the same pipeline data
2. Format the board briefing (more strategic, less tactical than the email report)
3. Send to the Board
4. Wait for response (or fire-and-forget with callback)
5. Store response and surface any URGENT items to Michael
