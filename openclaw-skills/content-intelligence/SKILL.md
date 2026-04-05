---
name: content-intelligence
description: Daily content intelligence pipeline for Modology Studios. Uses opencli-rs to pull trending content from HackerNews, Reddit, Twitter, and Stack Overflow — then synthesizes into actionable digests. Outputs: (1) daily Discord community digest, (2) Twitter thread ideas, (3) blog/content calendar suggestions. Use this skill to run the daily content pull, generate a digest, or plan the content calendar.
---

# Content Intelligence Pipeline

Pulls trending content from multiple sources via opencli-rs → synthesizes → distributes to community channels.

## OpenFang Integration
- **researcher-hand** (ID: `52a6d4df-6eb0-5c55-a200-b984514886ab`) handles deep research tasks
- **collector-hand** (ID: `5bb35868-43b9-5452-956d-cd6d908d5ded`) handles data collection
- OpenFang API: `http://localhost:4200/api/agents/{id}/message`

---

## Step 1: Pull Trending Content

Run these to gather raw signals:

```bash
# HackerNews — tech community pulse (no browser required)
opencli-rs hackernews top --limit 20 --format json

# HackerNews — Ask HN / Show HN (community questions and projects)
opencli-rs hackernews ask --limit 10 --format json
opencli-rs hackernews show --limit 10 --format json

# Reddit — self-hosted and AI communities
opencli-rs reddit subreddit --name "selfhosted" --format json --limit 20
opencli-rs reddit subreddit --name "LocalLLaMA" --format json --limit 20
opencli-rs reddit subreddit --name "MachineLearning" --format json --limit 10

# Stack Overflow — developer questions (no browser required)
opencli-rs stackoverflow hot --format json --limit 10

# Twitter — AI builder conversations (requires browser + login)
opencli-rs twitter search "self-hosted AI agents" --limit 20 --format json
opencli-rs twitter search "open source LLM 2026" --limit 15 --format json
opencli-rs twitter trending --format json

# Dev.to — developer articles (no browser required)
opencli-rs devto top --format json --limit 15
```

---

## Step 2: Synthesize via OpenFang researcher-hand

Send the collected data to researcher-hand for synthesis:

```bash
curl -s -X POST "http://localhost:4200/api/agents/52a6d4df-6eb0-5c55-a200-b984514886ab/message" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Synthesize this content into a daily digest for the ClawHQ Discord community. Focus on: (1) what builders are discussing about self-hosted AI, (2) pain points we can address, (3) 3 tweet thread ideas for @modologystudios. Content: [PASTE_JSON_HERE]"
  }'
```

---

## Step 3: Distribute

### Post digest to Discord
Use the Discord MCP or opencli-rs discord-app to post to `#daily-digest` or `#general`:

```bash
# Navigate to #digest channel in Discord desktop, then:
opencli-rs discord-app send "📰 Daily AI Digest — [DATE]

Top from the community:
• [item 1]
• [item 2]
• [item 3]

Full digest: [link or thread]"
```

### Queue Twitter content
Draft tweets/threads based on researcher-hand synthesis, then use the Twitter MCP:
```json
{ "action": "post", "text": "Thread: [synthesized insight] 🧵\n\n1/" }
```

---

## Automated Daily Digest (Cron)

To run daily at 8am:
```bash
# Run the full pipeline
opencli-rs hackernews top --limit 20 --format json > /tmp/hn.json
opencli-rs reddit subreddit --name "selfhosted" --format json --limit 20 > /tmp/reddit.json
opencli-rs devto top --format json --limit 10 > /tmp/devto.json

# Combine and send to researcher-hand
COMBINED=$(cat /tmp/hn.json /tmp/reddit.json /tmp/devto.json | python3 -c "
import json,sys
data = []
for line in sys.stdin:
    try:
        data.extend(json.loads(line) if isinstance(json.loads(line), list) else [json.loads(line)])
    except: pass
print(json.dumps(data[:30]))
")

curl -s -X POST "http://localhost:4200/api/agents/52a6d4df-6eb0-5c55-a200-b984514886ab/message" \
  -H "Content-Type: application/json" \
  -d "{\"message\": \"Daily content digest task: Analyze these trending items and create: (1) 5-bullet Discord digest, (2) 3 tweet ideas with hashtags for ClawHQ/OpenClaw. Items: $COMBINED\"}"
```

---

## Content Calendar Planning

Ask researcher-hand to plan weekly content:
```bash
curl -s -X POST "http://localhost:4200/api/agents/52a6d4df-6eb0-5c55-a200-b984514886ab/message" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Create a 7-day content calendar for ClawHQ/Modology Studios. Products: ClawHQ (self-hosted AI agent platform), OpenClaw (agent framework), GapHunter (competitor review intelligence), ClawCost (AI cost management). Channels: Twitter/X, Discord community. Format: day, platform, type (educational/product/community), draft content."
  }'
```

---

## Target Topics by Audience

| Audience | Topics to Monitor | Action |
|----------|-------------------|--------|
| AI Builders | self-hosted LLM, docker AI, agent frameworks | Engage with helpful replies |
| DevOps/Infra | docker compose, self-hosted tools | Share ClawHQ setup guides |
| Founders/Indie | build in public, MRR milestones | Community engagement, share journey |
| Product teams | G2 reviews, competitor analysis | GapHunter use cases |
| Cost-conscious teams | AI costs, token usage, API bills | ClawCost positioning |
