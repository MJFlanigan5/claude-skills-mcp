# Expert Web Research — SKILL.md
# Deep Research Skill for AI Agents | ClawMart Edition
# Version: 1.0.0

---

## OVERVIEW

This skill transforms an AI agent into a rigorous, structured research engine. It defines the methodology, source evaluation frameworks, evidence grading standards, and report formats needed to produce research that is verifiable, balanced, and actionable.

This is not a "search and summarize" skill. It is a multi-layered research methodology that separates signal from noise, grades evidence quality, tracks confidence explicitly, and produces reports a professional would stand behind.

---

## CORE PHILOSOPHY

**Research quality is determined before you write a single word of the report.**

The most common research failure is confirmation bias: finding three sources that agree and calling it research. Expert research:
1. Actively seeks disconfirming evidence
2. Grades sources, not just counts them
3. Separates facts from interpretations
4. States confidence levels explicitly
5. Identifies what it *doesn't* know

---

## TOOL REQUIREMENTS

This skill uses two primary tools:

```
web_search  — for broad discovery, finding sources, current events
web_fetch   — for reading full content from discovered URLs
```

### Tool Usage Guidelines

```python
# web_search: use for
# - Initial landscape mapping
# - Finding primary sources
# - Getting recent news/updates
# - Discovering competing viewpoints

web_search(
    query="[specific query]",
    count=10,           # max results per query
    freshness="pw"      # pw=past week, pm=past month, py=past year
)

# web_fetch: use for
# - Reading full articles (not just snippets)
# - Extracting data tables, pricing, specs
# - Verifying claims found in search snippets
# - Reading primary documents (reports, studies, filings)

web_fetch(
    url="https://example.com/article",
    extractMode="markdown",  # or "text" for cleaner extraction
    maxChars=15000           # adjust based on doc length
)
```

---

## PHASE 1: RESEARCH PLANNING

Before running a single search, complete the Research Brief.

### Research Brief Template

```markdown
## Research Brief

**Question:** [The core question to answer — be specific]
**Deliverable:** [What format, for whom, by when]
**Scope:** [What is IN scope vs explicitly OUT of scope]
**Success criteria:** [How will you know when you have a good answer?]

**Sub-questions:**
1. [Specific sub-question 1]
2. [Specific sub-question 2]
3. [Specific sub-question 3]

**Likely source types:**
- [ ] Academic / peer-reviewed
- [ ] Industry reports (Gartner, IDC, McKinsey, etc.)
- [ ] Government / regulatory filings
- [ ] News publications (major)
- [ ] Company announcements / press releases
- [ ] Expert blogs / practitioner content
- [ ] Forum discussions (Reddit, HN, Stack Overflow)
- [ ] Competitor / product documentation

**Known unknowns:**
- [What are you uncertain about before you start?]

**Disconfirming hypothesis:**
- [What would prove the opposite of what you expect to find?]
```

### Query Design Rules

**The Rule of Diversification: Never use just one query.**

```
For any research question, generate:
├── 1–2 direct queries (the obvious search)
├── 1–2 indirect queries (related terms, synonyms, adjacent topics)
├── 1 disconfirming query ("problems with X", "criticism of X", "X failure")
└── 1 expert/practitioner query ("X site:reddit.com", "X HN", "X case study")
```

**Query Patterns That Work:**

```bash
# Pattern 1: Specific over general
BAD:  "AI market size"
GOOD: "AI SaaS market size 2024 Gartner IDC report"

# Pattern 2: Include source type
"[topic] site:reuters.com OR site:bloomberg.com"
"[topic] filetype:pdf"
"[topic] inurl:report OR inurl:study OR inurl:whitepaper"

# Pattern 3: Expert opinion
"[topic] expert opinion [year]"
"[topic] interview founder CEO"
"what experts think about [topic]"

# Pattern 4: Disconfirming
"[topic] problems issues drawbacks"
"[topic] criticism skepticism"
"[topic] failure case study"
"why [topic] doesn't work"

# Pattern 5: Temporal anchoring
"[topic] 2024" or "[topic] Q1 2024"
"[topic] latest update"
"[topic] new development this year"

# Pattern 6: Comparison
"[topic A] vs [topic B] comparison"
"[topic] alternatives"
"[topic] competitor analysis"
```

---

## PHASE 2: SOURCE EVALUATION

### The CRAAP Test

Every source is evaluated on 5 dimensions before its content is used.

```
C — CURRENCY
    How recent is the information?
    Is the topic time-sensitive?
    Has information been updated or revised since?
    
    Score: 5 = published/updated within 3 months
           3 = published within 1 year
           1 = published within 3 years
           0 = older than 3 years (domain-dependent)

R — RELEVANCE  
    Does it directly address your research question?
    Is the depth appropriate (not too surface-level)?
    Is it written for your intended audience?
    
    Score: 5 = directly answers your question with depth
           3 = tangentially related, useful context
           1 = loosely connected, use with caution
           0 = off-topic, included only for completeness

A — AUTHORITY
    Who wrote this? What are their credentials?
    Is the author/org expert in this domain?
    Are they cited by others in the field?
    
    Score: 5 = Peer-reviewed, domain expert, established institution
           4 = Major publication (NYT, Bloomberg, Reuters, FT)
           3 = Industry publication, known practitioner blog
           2 = Company blog, press release (biased but useful)
           1 = Personal blog, forum post
           0 = Anonymous, unknown source

A — ACCURACY
    Are claims supported with data/citations?
    Can claims be verified elsewhere?
    Are methods described (for studies)?
    Does it distinguish fact from opinion?
    
    Score: 5 = Data-backed, cited, methodology disclosed
           3 = Claims supported but sources not cited
           1 = Assertions without evidence
           0 = Contradicted by more reliable sources

P — PURPOSE
    Why was this created?
    Inform? Persuade? Sell? Entertain?
    Is there a conflict of interest?
    
    Score: 5 = Neutral, informational, no COI
           3 = Slight bias but relevant
           1 = Strong advocacy, PR/marketing content
           0 = Propaganda, misleading content
```

**CRAAP Scoring:**
```
Total possible: 25 points

TIER A (20–25): Primary source — use directly, cite with confidence
TIER B (15–19): Secondary source — use with attribution, note limitations
TIER C (10–14): Supporting source — use for context only, note bias
TIER D (<10):   Discard or use only as "some claim that..." with caveat
```

---

## PHASE 3: EVIDENCE COLLECTION

### Evidence Entry Format

For each piece of evidence collected, log it in this format:

```markdown
### [EV-001] — [Short descriptor]

**Source:** [Title] — [URL]
**Author/Org:** [Name] | **Date:** [YYYY-MM-DD] | **CRAAP Score:** [X/25] (Tier [A/B/C/D])
**Type:** [Fact / Statistic / Expert Opinion / Case Study / Anecdote]
**Claim:** "[Verbatim quote or precise summary]"
**Supports:** [Which research sub-question does this address?]
**Contradicts:** [Any contradicting evidence found?]
**Confidence:** [High / Medium / Low]
**Notes:** [Context, caveats, limitations]
```

### Evidence Grading Scale

```
GRADE A — Hard evidence
  Definition: Independently verifiable data, peer-reviewed study, 
              official filing, primary source document
  Usage: State as fact with citation
  Example: "According to the 2023 10-K filing, revenue was $X billion"

GRADE B — Corroborated claim
  Definition: Claim made by credible source, corroborated by ≥1 independent source
  Usage: State as established with citation
  Example: "Multiple analysts estimate..."

GRADE C — Single credible source
  Definition: Claim from a Tier A or B source, not yet independently verified
  Usage: Attribute and note single-source limitation
  Example: "According to [source], though not independently confirmed..."

GRADE D — Anecdotal / Unverified
  Definition: Forum posts, personal accounts, uncited claims
  Usage: Note as anecdotal, use only for texture/signal
  Example: "Practitioners report... though this is anecdotal"

GRADE F — Contradicted / Unreliable
  Definition: Claims disproven by higher-grade evidence
  Usage: Document for completeness, flag explicitly
  Example: "Earlier reports claimed X; this has since been disproven by [source]"
```

---

## PHASE 4: CONFIDENCE SCORING

### Per-Claim Confidence

Apply a confidence score to each major claim in the final report:

```python
CONFIDENCE_LEVELS = {
    "CONFIRMED":    "≥3 independent Grade A/B sources agree",
    "HIGH":         "2+ credible sources, no contradiction found",
    "MEDIUM":       "1 credible source, or 2 lower-grade sources",
    "LOW":          "1 low-grade source, or conflicting evidence",
    "UNKNOWN":      "Insufficient evidence found",
    "CONTESTED":    "Credible sources actively disagree"
}
```

**Inline citation format for reports:**
```
[CONFIRMED] The global AI market is projected to reach $1.8 trillion by 2030. [EV-003, EV-007]
[HIGH] Adoption rates are highest in financial services and healthcare. [EV-012]
[MEDIUM] Enterprise deals typically take 6–18 months to close. [EV-019]  
[LOW] Some practitioners report implementation costs doubling projections. [EV-024]
[CONTESTED] Whether LLM reasoning constitutes "understanding" is actively debated. [EV-031, EV-033]
```

### Overall Research Confidence

At the end of research, score the overall finding:

```markdown
## Research Confidence Assessment

**Coverage:** [X%] of sub-questions answered with HIGH or CONFIRMED evidence
**Gaps:** [List unanswered questions and why]
**Contradictions:** [Any unresolved conflicts between sources?]
**Recency:** [How current is the evidence?]
**Bias check:** [Did you actively seek disconfirming evidence? What did you find?]

**Overall Confidence:** [CONFIRMED / HIGH / MEDIUM / LOW / INCONCLUSIVE]
**Reason:** [1–2 sentences explaining the score]
```

---

## PHASE 5: REPORT STRUCTURE

### The Inverted Pyramid Model

Structure all reports with the most important information first, detail deepening as the document progresses. A reader should be able to stop at any point and still have the essential answer.

```
LEVEL 1 — EXECUTIVE LAYER (top of report)
  What we found. The headline answer. Confidence level.
  Should be readable in under 60 seconds.

LEVEL 2 — ANALYTICAL LAYER (middle)
  Why. Supporting evidence. Key themes.
  The "how we got here" section for skeptical readers.

LEVEL 3 — EVIDENCE LAYER (bottom / appendix)
  Source-by-source documentation.
  Raw evidence log. Full citations.
  For readers who want to verify or go deeper.
```

### Standard Report Template

```markdown
# Research Report: [Topic]

**Prepared:** [Date]  
**Scope:** [What was researched]  
**Method:** [Brief summary of research approach]  
**Total sources evaluated:** [N] | **Sources used:** [N]  
**Overall confidence:** [Level] — [1-sentence reason]

---

## Executive Summary

**Core Finding:** [1–3 sentences. The headline answer.]

**Key Takeaways:**
1. [Takeaway 1] [CONFIDENCE LEVEL]
2. [Takeaway 2] [CONFIDENCE LEVEL]
3. [Takeaway 3] [CONFIDENCE LEVEL]

**Recommendation / Implication:** [What should the reader do with this?]

**Key Caveat:** [What's the most important limitation of this research?]

---

## Findings

### Finding 1: [Title]

[2–4 paragraph deep dive. Evidence cited inline with [EV-XXX] references.
Confidence tagged on key claims. Contradicting evidence acknowledged.]

**Supporting evidence:** [EV-001], [EV-005], [EV-008]  
**Confidence:** HIGH

---

### Finding 2: [Title]

[Same structure]

---

### Finding 3: [Title]

[Same structure]

---

## What We Don't Know

| Question | Status | Why |
|----------|--------|-----|
| [Q1] | Unanswered | No reliable sources found |
| [Q2] | Contested | Sources conflict — see EV-031 vs EV-033 |
| [Q3] | Outdated | Best source is 3+ years old |

---

## Methodology

**Queries run:** [N]  
**Sources evaluated:** [N]  
**Tier A sources:** [N] | **Tier B:** [N] | **Tier C:** [N] | **Discarded:** [N]  
**Disconfirming queries run:** [Yes/No — list them]  
**Date range of sources:** [Earliest] to [Most recent]  

---

## Evidence Log

### [EV-001] — [Descriptor]
[Full evidence entry using format from Phase 3]

### [EV-002] — [Descriptor]
[Full evidence entry]

[...continues...]

---

## Source Index

| ID | Title | URL | Date | CRAAP | Tier |
|----|-------|-----|------|-------|------|
| EV-001 | [Title] | [URL] | [Date] | [Score] | [A/B/C] |
```

---

## RESEARCH WORKFLOWS

### Workflow 1: Quick Research (30 min budget)
*Use for: fast fact-checks, single-question answers, supporting evidence for existing work*

```
Step 1: Define the exact question (5 min)
Step 2: Run 3–5 targeted queries
Step 3: web_fetch top 3 results
Step 4: CRAAP-score sources (quick pass, not full scoring)
Step 5: Write a SHORT findings note (not a full report)
         Format: "Based on [N] sources: [Finding]. 
                  Confidence: [Level]. Key source: [URL]"
```

### Workflow 2: Standard Research (2–4 hour budget)
*Use for: competitive analysis, market research, decision support*

```
Step 1: Complete Research Brief (10 min)
Step 2: Run 8–12 queries across all patterns
Step 3: web_fetch 8–15 sources
Step 4: Full CRAAP scoring on all sources
Step 5: Build evidence log (all Grade A/B entries)
Step 6: Write standard report (inverted pyramid)
Step 7: Run bias check — did you find disconfirming evidence?
Step 8: Score overall confidence
```

### Workflow 3: Deep Research (1–2 day budget)
*Use for: investment thesis, major strategic decisions, publish-quality work*

```
Step 1: Complete Research Brief with stakeholder review
Step 2: 20+ queries across all query patterns
Step 3: web_fetch 20–40 sources (mix of lengths)
Step 4: Full CRAAP scoring on all sources
Step 5: Build complete evidence log
Step 6: Identify gaps and run targeted follow-up queries
Step 7: Seek expert sources specifically for high-stakes claims
Step 8: Write full report with all sections
Step 9: Peer review by second agent or human
Step 10: Document methodology in full
```

---

## ANTI-PATTERNS TO AVOID

```
❌ CONFIRMATION SEARCH
   Only searching for evidence that supports the hypothesis.
   Fix: Run at least one explicit disconfirming query.

❌ SNIPPET RESEARCH  
   Drawing conclusions from search result snippets without fetching the full source.
   Fix: Always web_fetch for any source you're going to cite.

❌ RECENCY NEGLECT
   Using outdated sources for fast-moving topics.
   Fix: Check publish dates; filter for recency on time-sensitive topics.

❌ AUTHORITY CONFLATION
   Treating all credible-sounding sources as equal.
   Fix: Use CRAAP scoring; distinguish primary from secondary sources.

❌ PRECISION LAUNDERING
   Citing a precise statistic ("73% of companies...") without checking the original methodology.
   Fix: Always trace statistics to their primary source.

❌ SINGLE-SOURCE FACTS
   Presenting claims from one source as established fact.
   Fix: Label single-source claims; try to corroborate before promoting to Grade B.

❌ MISSING METADATA
   Pulling quotes without noting author, date, or publication.
   Fix: Log all metadata in the evidence entry at collection time.

❌ SCOPE CREEP
   Following interesting tangents that drift from the research question.
   Fix: Re-read the Research Brief before each new query batch.
```

---

## ADVANCED TECHNIQUES

### Triangulation
For any high-stakes claim, find it from at least 3 independent sources. The sources should differ in:
- Type (academic, journalistic, practitioner)
- Perspective (not three publications all citing the same original study)
- Time (not all from the same news cycle)

### Source Tracing
When a statistic appears in multiple places, trace it to its origin:
```
Found in: TechCrunch article → Cited: Gartner report → Traced to: Gartner survey methodology
Report if: TechCrunch misquoted or decontextualized the original
```

### Lateral Reading
Don't just read the source — read *about* the source.
```
For any unfamiliar publication or author:
1. web_search "[publication name] credibility bias"
2. web_search "[author name] background expertise"
3. Check: Is this publication known for accuracy in this domain?
```

### The Steel Man Test
Before concluding, articulate the strongest version of the opposing view:
```
My finding: [X]
Steel-manned opposition: The strongest argument against this finding is [Y].
Why I still conclude X: [Because Z evidence outweighs Y for these reasons...]
```

---

## QUERY QUICK REFERENCE

```bash
# Market sizing
"[market] market size [year] billion report"
"[market] TAM analysis [year]"

# Competitive landscape  
"[company] competitors alternatives"
"[company] vs [company2] comparison [year]"

# Pricing research
"[product] pricing plans cost [year]"
"[product] enterprise pricing"

# Technology assessment
"[technology] pros cons tradeoffs"
"[technology] limitations drawbacks"
"[technology] real-world case study"

# People / expert search
"[name] [expertise domain] interview"
"[name] [company] background"

# Data / statistics
"[claim] statistics data [year] site:statista.com OR site:ourworldindata.org"

# Primary documents
"[company] annual report 10-K [year] site:sec.gov"
"[org] research report [year] filetype:pdf"

# Recency filters
"[topic] after:2024-01-01"
"[topic] 2024"
"[topic] latest"
```

---

## SKILL ACTIVATION PROMPT

When this skill is loaded, the agent should announce its approach before beginning research:

```
I'll be using structured web research methodology for this:

1. Research planning (define scope, sub-questions, query strategy)
2. Multi-source discovery (varied query types, disconfirming queries included)
3. Source evaluation (CRAAP scoring on all sources)
4. Evidence grading (Grade A–F based on verifiability and sourcing)
5. Confidence scoring (per-claim and overall)
6. Structured report (inverted pyramid format)

Estimated time: [X] minutes
Sources target: [N] minimum
Will report: Overall confidence level and gaps

Starting now.
```

---

*Expert Web Research — Available on ClawMart*
*Deploy this SKILL.md to any OpenClaw agent to activate deep research mode.*
