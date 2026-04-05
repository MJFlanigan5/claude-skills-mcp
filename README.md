# Claude Skills & MCP Servers

A collection of Claude Code skills and MCP server configurations for use with Claude Code CLI and OpenClaw.

## Structure

```
├── claude-skills/          # Skills installed in Claude Code (~/.claude/skills/)
│   ├── brainstorming/
│   ├── crm-automation/
│   ├── discord/
│   ├── dispatching-parallel-agents/
│   ├── elite-design/
│   ├── enrich-lead/
│   ├── executing-plans/
│   ├── finishing-a-development-branch/
│   ├── gh-issues/
│   ├── innovation-scout/
│   ├── journey/
│   ├── market-research/
│   ├── product-discovery/
│   ├── receiving-code-review/
│   ├── requesting-code-review/
│   ├── subagent-driven-development/
│   ├── systematic-debugging/
│   ├── test-driven-development/
│   ├── user-research/
│   ├── using-git-worktrees/
│   ├── using-superpowers/
│   ├── verification-before-completion/
│   ├── video-lens/
│   ├── writing-plans/
│   └── writing-skills/
│
├── openclaw-skills/        # Skills installed via OpenClaw (~/.openclaw/workspace/skills/)
│   └── ... (147 skills)
│
└── mcp-servers/
    └── settings.json       # MCP server configs (API keys redacted — replace with your own)
```

## Claude Code Skills

25 skills installed in `~/.claude/skills/`. Each skill is a `SKILL.md` file that defines behavior, triggers, and instructions for Claude.

Install skills via [Claude Code skill marketplace](https://claude.ai/code) or manually place the `SKILL.md` in `~/.claude/skills/<skill-name>/`.

## OpenClaw Skills

147 skills from the [OpenClaw](https://openclaw.dev) agent platform. These extend Claude with domain-specific capabilities across:

- **Agent orchestration**: agent-team-orchestration, agent-watcher, agentic-coding
- **APIs & Dev**: api-dev, api-gateway, api-generator, api-tester
- **Automation**: automation-workflows, autoresearch, ez-cronjob
- **Business/Growth**: apex-growth, cmo, competitor-monitoring, growth-hacker
- **Design**: canvas-design, design-review, elite-design, graphic-design, ui-ux-design
- **Lead Gen**: build-lead-scoring, lead-enrichment, lead-signals, outreach-engine
- **Marketing**: marketing-analytics, master-marketing, seo-content-writer
- **Productivity**: personal-productivity, productivity-automation-kit
- **Research**: autoresearch, competitor-teardown, market-research
- **Sales**: sales-funnel-design, stripe-analytics
- ...and many more

## MCP Servers

The `mcp-servers/settings.json` contains configurations for these MCP servers:

| Server | Package | Description |
|--------|---------|-------------|
| playwright | `@playwright/mcp` | Browser automation, scraping, E2E testing |
| notion | `@notionhq/notion-mcp-server` | Notion workspace integration |
| stripe | `@cloud9-labs/mcp-stripe` | Stripe payments and revenue data |
| figma | `figma-context-mcp` | Figma design files and components |
| context7 | `@upstash/context7-mcp` | Live library documentation |
| cloudflare | `@cloudflare/mcp-server-cloudflare` | Cloudflare deployments and DNS |
| google-analytics | `@iflow-mcp/mcp-server-google-analytics` | GA traffic data |
| scraper | `scrapeless-mcp-server` | Web scraping |
| github | `@github/github-mcp-server` | GitHub repos, issues, PRs |
| homey | `https://mcp.athom.com` | Homey Pro smart home |

### Setup

1. Copy `mcp-servers/settings.json` into your `~/.claude/settings.json` (merge with existing)
2. Replace all `YOUR_*` placeholders with your actual API keys
3. Restart Claude Code

## Usage

For Claude Code skills, invoke with `/skill-name` in any Claude Code session.

For OpenClaw skills, install via the OpenClaw CLI:
```bash
npx openclaw install <skill-name>
```
