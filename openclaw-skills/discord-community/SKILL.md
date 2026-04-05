---
name: discord-community
description: Discord community management via two complementary approaches: (1) opencli-rs discord-app — controls your Discord desktop app for human-like community engagement (reading, posting, searching as yourself); (2) Discord MCP bot — programmatic bot operations via API token. Use this skill for community engagement, monitoring conversations, welcoming members, posting announcements, and responding to questions in the Modology Studios / ClawHQ community servers.
---

# Discord Community Management

Two complementary tools for Discord community work:

| Tool | Best For | Requires |
|------|----------|----------|
| `opencli-rs discord-app` | Community engagement as yourself (human-like) | Discord desktop app open + Chrome extension |
| `discord` MCP tool | Bot operations, automated responses | Discord bot token (pre-configured) |

---

## Tool 1: opencli-rs discord-app (Desktop App Control)

Controls your Discord desktop app via Chrome DevTools Protocol. Appears as your user account, not a bot.

### Prerequisites
- Discord desktop app running and logged in
- Chrome extension installed and connected (`opencli-rs doctor` to verify)

### Check connection
```bash
opencli-rs discord-app status
```

### List servers and channels
```bash
# See all servers you're in
opencli-rs discord-app servers --format json

# List channels in the active server
opencli-rs discord-app channels --format json
```

### Read messages
```bash
# Read last 20 messages from active channel
opencli-rs discord-app read --count 20 --format json

# Read more messages
opencli-rs discord-app read --count 50 --format json
```

### Send a message
```bash
# Navigate to the channel in Discord desktop first, then:
opencli-rs discord-app send "Welcome to the ClawHQ community! 👋"
```

### Search conversations
```bash
# Search for keywords across the server
opencli-rs discord-app search "self-hosted" --format json
opencli-rs discord-app search "ClawHQ" --format json
opencli-rs discord-app search "setup help" --format json
```

### List online members
```bash
opencli-rs discord-app members --format json
```

---

## Tool 2: Discord MCP (Bot API)

Pre-configured via openclaw.json. Use the `discord` channel or message tool.

### Post to a channel (via agent message)
Route a message through the Discord channel in OpenClaw. The bot token is configured in `openclaw.json`.

### Common bot operations
```json
{ "action": "send", "channel": "general", "text": "Message here" }
{ "action": "read", "channel": "general", "limit": 20 }
{ "action": "notifications", "limit": 10 }
```

---

## Community Management Workflows

### Daily check-in routine
```bash
# 1. Check what's been said overnight
opencli-rs discord-app search "help" --format json
opencli-rs discord-app search "error" --format json
opencli-rs discord-app search "question" --format json

# 2. Read the most active channels
opencli-rs discord-app read --count 50 --format json

# 3. Respond to any unanswered questions
opencli-rs discord-app send "Great question! Here's how to..."
```

### Welcome new members
```bash
# Read member list to spot newcomers
opencli-rs discord-app members --format json

# Send welcome message in #welcome or #general
opencli-rs discord-app send "Welcome @newmember! Drop any questions in #support — we're happy to help get you set up."
```

### Monitor for ClawHQ mentions
```bash
# Search for product mentions
opencli-rs discord-app search "ClawHQ" --format json
opencli-rs discord-app search "OpenClaw" --format json
opencli-rs discord-app search "install" --format json
```

### Post an announcement
```bash
# Navigate to #announcements in Discord desktop, then:
opencli-rs discord-app send "🚀 ClawHQ v1.x is live! New features: [list them]. Update: docker compose pull && docker compose up -d"
```

---

## Community Strategy

### Channels to monitor daily
- `#general` — general questions and conversation
- `#support` — installation and usage help
- `#showcase` — user demos and builds
- `#feedback` — product suggestions

### Engagement guidelines
- Respond to questions within 24 hours
- Celebrate user wins ("that's a great use case!")
- Share relevant docs/links, don't just answer verbally
- Escalate bug reports to GitHub issues
- Never argue — acknowledge, investigate, update

### Brand voice (same as Twitter)
- Direct and technical — builders talking to builders
- Helpful without being corporate
- Open source values: transparent, community-first
- "We" over "I" — reinforce it's a team/community

---

## Troubleshooting

### opencli-rs discord-app not connecting
```bash
# Check daemon is running
opencli-rs doctor

# Discord desktop must be open and active
# Chrome extension must be installed and enabled
# Try restarting the opencli-rs daemon:
pkill -f opencli-rs && sleep 2 && opencli-rs doctor
```

### Message send fails
- Make sure you've navigated to the target channel in Discord desktop before calling `send`
- The `send` command types into whichever channel is currently active

### Bot 403/permission errors
- Verify the bot token in `openclaw.json` → `mcp.servers.discord.env.DISCORD_TOKEN`
- Bot must have appropriate channel permissions in the server settings
