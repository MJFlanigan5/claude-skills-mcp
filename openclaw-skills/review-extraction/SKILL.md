---
name: review-extraction
description: Automated review extraction from multiple platforms. Extracts customer reviews, ratings, and sentiment for competitive analysis and gap identification.
metadata: {"clawdbot":{"emoji":"⭐","os":["linux","darwin","win32"]}}
---

# Review Extraction Skill

Automated extraction of customer reviews from various platforms for competitive analysis and gap identification.

## Prerequisites

1. **Browser-use CLI** must be installed and working
2. **Python 3.11+** required for browser-use
3. **Configuration file** at `~/.openclaw/workspace/config/review-sources.json`

## Quick Start

```bash
# Extract reviews from all configured sources
python3 ~/.openclaw/workspace/scripts/review-extractor.py --extract-all

# Extract from specific platform
python3 ~/.openclaw/workspace/scripts/review-extractor.py --platform "Trustpilot"

# Analyze sentiment and extract insights
python3 ~/.openclaw/workspace/scripts/review-extractor.py --analyze

# Generate gap analysis report
python3 ~/.openclaw/workspace/scripts/review-extractor.py --gap-analysis
```

## Supported Platforms

### Review Platforms
- **Trustpilot** - Business reviews
- **G2** - Software reviews  
- **Capterra** - Software reviews
- **Google Reviews** - Business reviews
- **Yelp** - Business reviews
- **Product Hunt** - Product reviews
- **App Store** - Mobile app reviews
- **Play Store** - Android app reviews

### E-commerce Platforms
- **Amazon** - Product reviews
- **Shopify** - Store reviews
- **Etsy** - Handmade product reviews

## Configuration

Create `~/.openclaw/workspace/config/review-sources.json`:

```json
{
  "sources": [
    {
      "platform": "Trustpilot",
      "url": "https://www.trustpilot.com/review/competitor.com",
      "selectors": {
        "item_selector": ".review-card",
        "rating": ".star-rating",
        "text": ".review-content__text",
        "author": ".consumer-information__name",
        "date": ".review-content-header__dates",
        "title": ".review-content__title"
      },
      "extraction_frequency": "weekly",
      "max_pages": 5
    },
    {
      "platform": "G2",
      "url": "https://www.g2.com/products/competitor/reviews",
      "selectors": {
        "item_selector": ".review-item",
        "rating": ".rating-stars",
        "text": ".review-body",
        "author": ".reviewer-name",
        "date": ".review-date",
        "pros": ".pros-list",
        "cons": ".cons-list"
      },
      "extraction_frequency": "monthly",
      "max_pages": 3
    }
  ],
  "storage": {
    "data_dir": "~/.openclaw/workspace/data/reviews",
    "retention_days": 365
  },
  "analysis": {
    "sentiment_enabled": true,
    "topic_modeling_enabled": true,
    "gap_detection_enabled": true
  }
}
```

## Core Workflow

### 1. Initial Setup
```bash
# Create configuration directory
mkdir -p ~/.openclaw/workspace/config
mkdir -p ~/.openclaw/workspace/data/reviews

# Generate template config
python3 ~/.openclaw/workspace/scripts/review-extractor.py --generate-config
```

### 2. Test Extraction
```bash
# Test extraction for a platform
python3 ~/.openclaw/workspace/scripts/review-extractor.py --test --platform "Trustpilot"
```

### 3. Run First Extraction
```bash
# Extract reviews from all sources
python3 ~/.openclaw/workspace/scripts/review-extractor.py --extract-all
```

### 4. Analyze Results
```bash
# Run sentiment analysis
python3 ~/.openclaw/workspace/scripts/review-extractor.py --sentiment-analysis

# Generate topic clusters
python3 ~/.openclaw/workspace/scripts/review-extractor.py --topic-clustering
```

## Data Storage

Review data is stored in structured format:

```
~/.openclaw/workspace/data/reviews/
├── trustpilot/
│   ├── 2026-03-29.json
│   ├── 2026-03-28.json
│   └── analysis/
│       ├── sentiment.json
│       ├── topics.json
│       └── gaps.json
├── g2/
│   ├── 2026-03-29.json
│   └── analysis/
└── combined/
    ├── all-reviews.csv
    └── insights.json
```

## Analysis Features

### Sentiment Analysis
- Positive/negative/neutral classification
- Emotion detection (anger, joy, sadness, etc.)
- Star rating correlation
- Trend analysis over time

### Topic Modeling
- Automatic topic discovery
- Keyword extraction
- Theme clustering
- Pain point identification

### Gap Analysis
- Feature requests identification
- Competitor weaknesses
- Market opportunities
- Customer pain points

## Integration with Gaphunter

### Direct Integration
```bash
# Export reviews to Gaphunter format
python3 ~/.openclaw/workspace/scripts/review-extractor.py --export-for-gaphunter

# Generate gap analysis report
python3 ~/.openclaw/workspace/scripts/review-extractor.py --gap-report
```

### Competitive Intelligence
```bash
# Compare reviews across competitors
python3 ~/.openclaw/workspace/scripts/review-extractor.py --compare-competitors

# Generate competitive positioning report
python3 ~/.openclaw/workspace/scripts/review-extractor.py --positioning-report
```

## Advanced Features

### Real-time Monitoring
```bash
# Set up monitoring for new reviews
python3 ~/.openclaw/workspace/scripts/review-extractor.py --setup-monitoring

# Check for new reviews since last extraction
python3 ~/.openclaw/workspace/scripts/review-extractor.py --check-new
```

### Alert System
```bash
# Get alerts for negative review spikes
python3 ~/.openclaw/workspace/scripts/review-extractor.py --setup-alerts

# Monitor specific keywords
python3 ~/.openclaw/workspace/scripts/review-extractor.py --keyword-alerts "bug,crash,slow"
```

### Export and Reporting
```bash
# Export to CSV for spreadsheet analysis
python3 ~/.openclaw/workspace/scripts/review-extractor.py --export-csv

# Generate PDF report
python3 ~/.openclaw/workspace/scripts/review-extractor.py --generate-report

# Email weekly summary
python3 ~/.openclaw/workspace/scripts/review-extractor.py --email-summary team@modologystudios.com
```

## Use Cases

### For Gaphunter
1. **Competitor gap identification** - Find what customers complain about
2. **Feature opportunity discovery** - Identify requested features
3. **Pricing feedback** - Understand price sensitivity
4. **UX pain points** - Identify usability issues

### For Product Development
1. **Prioritization** - Based on customer feedback frequency
2. **Bug tracking** - Monitor recurring issues
3. **Feature validation** - Test new feature ideas
4. **Roadmap planning** - Data-driven decision making

### For Marketing
1. **Testimonial extraction** - Find positive quotes
2. **Competitive positioning** - Highlight strengths vs competitors
3. **Content ideas** - Address common questions
4. **SEO opportunities** - Target review keywords

## Best Practices

### Ethical Considerations
- **Respect rate limits** - Add delays between requests
- **Check robots.txt** - Before scraping any site
- **Use official APIs** - When available
- **Limit data collection** - Only collect what you need
- **Respect privacy** - Anonymize personal data

### Technical Best Practices
1. **Rotate user agents** - Avoid detection
2. **Use proxies** - For large-scale extraction
3. **Implement retries** - Handle temporary failures
4. **Validate data** - Check for extraction errors
5. **Monitor success rates** - Adjust selectors as needed

### Data Management
1. **Regular backups** - Of configuration and data
2. **Data cleaning** - Remove duplicates and invalid entries
3. **Version control** - For configuration files
4. **Documentation** - Keep selectors and patterns documented

## Troubleshooting

### Common Issues

1. **Selectors not working**
   ```bash
   # Enable debug mode
   python3 ~/.openclaw/workspace/scripts/review-extractor.py --debug --platform "Trustpilot"
   
   # Save page HTML for inspection
   python3 ~/.openclaw/workspace/scripts/review-extractor.py --save-html
   ```

2. **Rate limiting**
   ```bash
   # Increase delays
   python3 ~/.openclaw/workspace/scripts/review-extractor.py --delay 10
   
   # Use different browser profiles
   python3 ~/.openclaw/workspace/scripts/review-extractor.py --rotate-profiles
   ```

3. **JavaScript-heavy sites**
   ```bash
   # Wait longer for JavaScript to load
   python3 ~/.openclaw/workspace/scripts/review-extractor.py --wait-time 5
   
   # Use headed browser for debugging
   python3 ~/.openclaw/workspace/scripts/review-extractor.py --headed
   ```

### Debug Mode
```bash
# Run with verbose logging
python3 ~/.openclaw/workspace/scripts/review-extractor.py --verbose

# Save screenshots at each step
python3 ~/.openclaw/workspace/scripts/review-extractor.py --save-screenshots

# Test individual selectors
python3 ~/.openclaw/workspace/scripts/review-extractor.py --test-selector ".review-card"
```

## Performance Optimization

### For Large-scale Extraction
```bash
# Use parallel extraction
python3 ~/.openclaw/workspace/scripts/review-extractor.py --parallel 4

# Cache page responses
python3 ~/.openclaw/workspace/scripts/review-extractor.py --enable-cache

# Resume interrupted extractions
python3 ~/.openclaw/workspace/scripts/review-extractor.py --resume
```

### Memory Management
```bash
# Process in batches
python3 ~/.openclaw/workspace/scripts/review-extractor.py --batch-size 100

# Stream to disk instead of memory
python3 ~/.openclaw/workspace/scripts/review-extractor.py --stream-to-disk
```

## Legal Compliance

### GDPR Considerations
- **Right to be forgotten** - Implement data deletion
- **Data minimization** - Only collect necessary data
- **Consent** - For personal data processing
- **Transparency** - Document data collection practices

### Terms of Service
- **Review platform ToS** - Before scraping
- **Rate limiting** - Adhere to platform limits
- **Commercial use** - Check if allowed
- **Attribution** - Give credit when required

## Support and Updates

### Getting Help
```bash
# Check system status
python3 ~/.openclaw/workspace/scripts/review-extractor.py --status

# View logs
tail -f ~/.openclaw/workspace/logs/review-extractor.log

# Run diagnostics
python3 ~/.openclaw/workspace/scripts/review-extractor.py --diagnose
```

### Updates
```bash
# Check for updates
python3 ~/.openclaw/workspace/scripts/review-extractor.py --check-updates

# Update configuration templates
python3 ~/.openclaw/workspace/scripts/review-extractor.py --update-templates
```