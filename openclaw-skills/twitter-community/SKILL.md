---
name: twitter-community
description: Use when managing Twitter/X community engagement — posting tweets, replying to mentions, liking/retweeting relevant content, following target accounts, searching conversations, and tracking trending topics relevant to Modology Studios and its products (ClawHQ, GapHunter, ClawCost, OpenClaw).
---

# Twitter Community Management

You have access to the `twitter` MCP tool for all Twitter/X operations.

## Authentication

### Primary: Twitter MCP (OAuth 1.0a — API-based)
OAuth 1.0a credentials are pre-configured. All read operations work immediately.
**Write operations (post, reply, like, retweet, follow) require "Read and Write" app permissions.**
If a post/reply returns a 403 error, the Twitter app needs permissions updated at:
https://developer.twitter.com/en/portal/dashboard → App Settings → User authentication settings → Read and Write

### Fallback: opencli-rs twitter (Browser-based)
If the MCP is unavailable or write operations are blocked by API permissions, use opencli-rs as a fallback.
Requires: Chrome with extension installed + Twitter logged in.
```bash
opencli-rs twitter post "Your tweet here"
opencli-rs twitter reply --tweet-url "https://x.com/..." "Your reply"
opencli-rs twitter like --tweet-url "https://x.com/..."
opencli-rs twitter search "self-hosted AI" --limit 20 --format json
opencli-rs twitter timeline --format json
opencli-rs twitter trending --format json
opencli-rs twitter follow --username "targetuser"
```

## Core Operations

### Post a tweet
```json
{ "action": "post", "text": "Your tweet content here" }
```
- Max 280 characters
- Use line breaks for readability
- Include relevant hashtags: #OpenSource #AIAgents #SelfHosted #ClawHQ

### Reply to a tweet
```json
{ "action": "reply", "tweetId": "123456789", "text": "Your reply" }
```

### Like a tweet
```json
{ "action": "like", "tweetId": "123456789" }
```

### Retweet with comment (quote tweet)
```json
{ "action": "quote", "tweetId": "123456789", "text": "Your comment" }
```

### Search conversations
```json
{ "action": "search", "query": "self-hosted AI agents", "limit": 20 }
```

### Get trending topics
Use the `x-trends` tool for trending topics without API quota:
```bash
x-trends --country us --limit 10 --json
```

### Get mentions/notifications
```json
{ "action": "notifications", "limit": 20 }
```

### Follow/unfollow account
```json
{ "action": "follow", "username": "targetuser" }
```

### Get timeline
```json
{ "action": "timeline", "limit": 20 }
```

## Community Management Strategy

### Target conversations to engage with
Search for these topics and engage thoughtfully:
- `"self-hosted AI"` — our core audience
- `"docker compose AI"` — deployment audience
- `"open source LLM agents"` — technical community
- `"AI cost management"` — ClawCost audience
- `"competitor reviews G2"` or `"G2 reviews scraping"` — GapHunter audience
- `"OpenClaw"` — direct brand mentions
- `"Claude AI self-hosted"` — Anthropic user crossover

### Engagement guidelines
- Reply to questions about self-hosted AI with helpful, non-promotional answers
- Like and quote-tweet content from AI builders and indie hackers
- Post product updates with clear value props, not hype
- Engage with @AnthropicAI, @OpenAI, and AI developer community posts
- Thread format performs better than single tweets for technical content

### Brand voice
- Direct and technical — we're builders talking to builders
- Confident but not arrogant
- Open source values: transparency, community, no lock-in
- Product-led: show don't tell, link to demos and GitHub

## Hashtags Reference
Primary: `#ClawHQ` `#OpenClaw` `#OpenFang` `#SelfHosted` `#AIAgents`
Secondary: `#OpenSource` `#Docker` `#LLM` `#BuildInPublic` `#IndieHacker`
Topic-specific: `#GapHunter` `#ClawCost` `#AIcost` `#ReviewIntelligence`
