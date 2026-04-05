---
name: competitor-price-tracking
description: Automated competitor price monitoring and tracking. Extracts pricing data from competitor websites, tracks changes over time, and alerts on significant price movements.
metadata: {"clawdbot":{"emoji":"💰","os":["linux","darwin","win32"]}}
---

# Competitor Price Tracking Skill

Automated monitoring of competitor pricing with change detection and alerts.

## Prerequisites

1. **Browser-use CLI** must be installed and working
2. **Python 3.11+** required for browser-use
3. **Configuration file** at `~/.openclaw/workspace/config/competitor-prices.json`

## Quick Start

```bash
# Run a price check on all configured competitors
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --check-all

# Check specific competitor
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --competitor "Acme Corp"

# Generate price change report
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --report

# Set up daily monitoring cron job
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --setup-cron
```

## Configuration

Create `~/.openclaw/workspace/config/competitor-prices.json`:

```json
{
  "competitors": [
    {
      "name": "Competitor A",
      "url": "https://competitor-a.com/products",
      "selectors": {
        "item_selector": ".product-item",
        "name": ".product-name",
        "price": ".price",
        "description": ".product-description",
        "sku": ".sku"
      },
      "check_frequency": "daily",
      "alert_threshold": 0.1
    },
    {
      "name": "Competitor B", 
      "url": "https://competitor-b.com/store",
      "selectors": {
        "item_selector": ".store-item",
        "name": ".item-title",
        "price": ".current-price",
        "old_price": ".original-price",
        "discount": ".discount-badge"
      },
      "check_frequency": "weekly",
      "alert_threshold": 0.15
    }
  ],
  "storage": {
    "data_dir": "~/.openclaw/workspace/data/competitor-prices",
    "retention_days": 90
  },
  "alerts": {
    "discord_webhook": "https://discord.com/api/webhooks/...",
    "email": "alerts@modologystudios.com",
    "min_price_change_percent": 5.0
  }
}
```

## Core Workflow

### 1. Initial Setup
```bash
# Create configuration directory
mkdir -p ~/.openclaw/workspace/config
mkdir -p ~/.openclaw/workspace/data/competitor-prices

# Generate template config
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --generate-config
```

### 2. Test Selectors
```bash
# Test extraction for a competitor
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --test-selectors --competitor "Competitor A"
```

### 3. Run First Collection
```bash
# Collect initial price data
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --collect
```

### 4. Set Up Monitoring
```bash
# Set up daily cron job (runs at 3 AM)
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --setup-cron
```

## Data Storage

Price data is stored in CSV format with timestamp:

```
~/.openclaw/workspace/data/competitor-prices/
├── competitor-a/
│   ├── 2026-03-29.csv
│   ├── 2026-03-28.csv
│   └── summary.json
├── competitor-b/
│   ├── 2026-03-29.csv
│   └── summary.json
└── alerts/
    └── 2026-03-29.json
```

## Alert Triggers

The system alerts on:

1. **Price drops > threshold%** - Competitive threat
2. **Price increases > threshold%** - Market opportunity  
3. **New products** - Competitive expansion
4. **Discontinued products** - Market gaps
5. **Stock status changes** - Supply chain insights

## Integration with Other Skills

### With Competitor Monitoring Skill
```bash
# Update competitor dossiers with latest pricing
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --update-dossiers
```

### With Outreach Engine
```bash
# Generate leads based on price changes
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --generate-leads
```

### With Gaphunter
```bash
# Feed price data into gap analysis
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --export-for-gaphunter
```

## Advanced Features

### Price Trend Analysis
```bash
# Analyze 30-day price trends
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --analyze-trends --days 30

# Generate visualization
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --visualize
```

### Competitive Intelligence Reports
```bash
# Generate weekly competitive report
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --weekly-report

# Email report to team
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --send-report --email team@modologystudios.com
```

### Price Optimization Suggestions
```bash
# Get pricing recommendations based on competitor data
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --optimization-suggestions
```

## Troubleshooting

### Common Issues

1. **Selectors not working**
   ```bash
   # Enable debug mode to see what's being extracted
   python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --debug --competitor "Competitor A"
   ```

2. **Browser-use not installed**
   ```bash
   # Check installation
   browser-use doctor
   
   # Install if missing
   curl -fsSL https://browser-use.com/cli/install.sh | bash
   ```

3. **Rate limiting**
   ```bash
   # Add delays between requests
   python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --delay 5
   ```

### Debug Mode
```bash
# Run with verbose logging
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --verbose

# Save browser screenshots for debugging
python3 ~/.openclaw/workspace/scripts/competitor-price-tracker.py --save-screenshots
```

## Best Practices

1. **Respect robots.txt** - Check each site's robots.txt before scraping
2. **Add delays** - Minimum 2-3 seconds between requests
3. **Rotate user agents** - Use different browser profiles
4. **Monitor success rates** - Track extraction success and adjust selectors
5. **Validate data** - Check for missing or anomalous price data
6. **Backup configuration** - Regular backups of config and data

## Ethical Considerations

- Only scrape publicly available data
- Respect rate limits and terms of service
- Use data for competitive analysis, not replication
- Consider using official APIs when available
- Be transparent about data collection practices