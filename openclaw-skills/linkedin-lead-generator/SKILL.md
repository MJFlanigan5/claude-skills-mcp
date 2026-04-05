---
name: linkedin-lead-generator
description: LinkedIn lead generation skill for OpenClaw. Uses manual browser login for authentication and scrapes profiles, companies, and jobs. Exports leads to OpenFang Bridge format for automated processing. Use when: searching for B2B leads on LinkedIn, generating prospect lists, finding companies, or exporting leads to sales pipeline.
---

# LinkedIn Lead Generator Skill

A skill for generating B2B leads from LinkedIn using manual browser authentication and scraping.

## Features

- **Manual Browser Login**: Opens LinkedIn login page for user to authenticate manually (handles 2FA, captcha)
- **Profile Search**: Search for people by keywords, location, industry
- **Company Search**: Find companies by name, industry, location
- **Profile Scraping**: Extract detailed profile information
- **Lead Export**: Export leads to CSV format for OpenFang Bridge processing
- **Cookie Persistence**: Saves session cookies for reuse (30-day validity)

## Quick Job Signal Search (No Login Required)

Use `opencli-rs linkedin search` for job posting signals — companies hiring AI/automation roles are buyers:

```bash
# Companies actively building AI teams = high-intent ClawHQ prospects
opencli-rs linkedin search "AI agent engineer" --limit 20 --format json
opencli-rs linkedin search "LLM infrastructure" --location "United States" --limit 20 --format json
opencli-rs linkedin search "AI automation developer" --limit 15 --format json
```

Export job signals directly to OpenFang:
```bash
opencli-rs linkedin search "AI agent engineer" --limit 20 --format json > /tmp/linkedin_jobs.json
# Then route to lead-hand: curl -X POST http://localhost:4200/api/agents/f8bbf23d-820b-5f08-b291-313cd88d2ffc/message
```

See the `lead-signals` skill for the full pipeline.

---

## Authentication Method (Full Profile Scraping)

**Manual Login Approach:**
1. Skill opens browser to LinkedIn login page
2. User manually logs in (handles 2FA, captcha, security challenges)
3. Skill extracts and saves cookies to workspace directory
4. Cookies are reused for subsequent requests (valid for 30 days)
5. When cookies expire, prompts for re-authentication

## Tools

### `linkedin_login`
Opens browser for manual LinkedIn login. Saves session cookies for future use.

**Parameters:**
- `headless` (optional): Show browser window (default: false for manual login)

**Usage:**
```
linkedin_login
```

### `linkedin_search_profiles`
Search for LinkedIn profiles by keywords and location.

**Parameters:**
- `keywords`: Search terms (e.g., "CEO software company")
- `location` (optional): Geographic location (e.g., "New York")
- `limit` (optional): Maximum results (default: 10, max: 50)

**Usage:**
```
linkedin_search_profiles --keywords "CTO SaaS" --location "San Francisco" --limit 20
```

### `linkedin_get_profile`
Get detailed information for a specific LinkedIn profile.

**Parameters:**
- `profile_url`: LinkedIn profile URL or username
- `sections` (optional): Comma-separated sections to extract (experience,education,skills,etc.)

**Usage:**
```
linkedin_get_profile --profile_url "https://linkedin.com/in/johndoe"
```

### `linkedin_search_companies`
Search for companies on LinkedIn.

**Parameters:**
- `keywords`: Company name or industry keywords
- `location` (optional): Geographic location
- `limit` (optional): Maximum results (default: 10)

**Usage:**
```
linkedin_search_companies --keywords "software development" --location "Austin"
```

### `linkedin_export_leads`
Export leads to CSV format for OpenFang Bridge processing.

**Parameters:**
- `leads`: Array of lead objects or search criteria
- `filename` (optional): Custom filename (default: linkedin-leads-{timestamp}.csv)

**Usage:**
```
linkedin_export_leads --leads "[{name: 'John Doe', company: 'Acme Inc'}]"
```

### `linkedin_check_session`
Check if current LinkedIn session is valid.

**Usage:**
```
linkedin_check_session
```

### `linkedin_logout`
Clear saved LinkedIn session cookies.

**Usage:**
```
linkedin_logout
```

## Data Export Format

Exports leads in CSV format compatible with OpenFang Bridge:

```csv
name,email,company,title,location,linkedin_url,score,priority,timestamp
John Doe,john@example.com,Acme Inc,CEO,New York,https://linkedin.com/in/johndoe,75,high,2026-03-26T23:47:00Z
Jane Smith,jane@tech.com,TechCorp,CTO,San Francisco,https://linkedin.com/in/janesmith,85,high,2026-03-26T23:47:00Z
```

**Export Location:** `/Users/mflanigan/.openfang/data/lead-hand/exports/`

## Integration with OpenFang Bridge

1. Skill exports CSV files to OpenFang Bridge exports directory
2. OpenFang Bridge file watcher detects new files
3. Bridge processes leads, applies scoring algorithms
4. Leads are routed to appropriate agents via Paperclip integration
5. Revenue dashboard tracks LinkedIn-sourced leads and conversions

## Configuration

**Cookie Storage:** `~/.openclaw/workspace/linkedin-cookies.json`

**Default Settings:**
- Rate limiting: 2-second delay between requests
- Max results per search: 50
- Session validity: 30 days
- Export directory: `/Users/mflanigan/.openfang/data/lead-hand/exports/`

## Error Handling

- **Authentication failures**: Prompts for re-login
- **Rate limiting**: Exponential backoff with retries
- **Network errors**: Retry with increasing delays
- **Scraping failures**: Fallback to alternative selectors
- **Session expiry**: Automatic re-authentication prompt

## Usage Examples

### Basic Lead Generation Workflow:
```
1. linkedin_login
2. linkedin_search_profiles --keywords "VP Sales SaaS" --location "United States" --limit 30
3. linkedin_export_leads
```

### Company Research:
```
1. linkedin_login
2. linkedin_search_companies --keywords "AI startup" --location "San Francisco"
3. For each company: linkedin_get_profile for key executives
4. linkedin_export_leads
```

### Batch Processing:
```
# Generate 100 leads from multiple searches
linkedin_search_profiles --keywords "founder" --limit 25
linkedin_search_profiles --keywords "director engineering" --limit 25
linkedin_search_profiles --keywords "product manager" --limit 25
linkedin_search_profiles --keywords "marketing director" --limit 25
linkedin_export_leads
```

## Business Impact

**Estimated Revenue Contribution:**
- 50-100 qualified leads per day
- 2-5% conversion rate (B2B average)
- $500-$2000 MRR per customer
- **Potential:** $5,000-$20,000 monthly MRR from LinkedIn

**Alignment with $30K MRR Target:**
- Could provide 33-66% of target revenue
- High ROI on development time (4-8 hours)
- Scalable as business grows

## Troubleshooting

### Common Issues:

1. **Login fails**: Clear cookies and retry (`linkedin_logout` then `linkedin_login`)
2. **Rate limited**: Wait 1 hour, reduce search frequency
3. **Scraping fails**: LinkedIn may have changed page structure
4. **Session expired**: Re-authenticate with `linkedin_login`

### Debug Commands:
```
linkedin_check_session  # Verify session validity
linkedin_logout         # Clear all cookies
```

## Development Notes

**Built with:** OpenClaw browser tool, manual authentication pattern
**Last Updated:** March 26, 2026
**Version:** 1.0.0
**Maintainer:** Felix (Modology Studios)