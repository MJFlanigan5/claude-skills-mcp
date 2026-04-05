# User Research & Survey Toolkit

## Description
Design, execute, and analyze user research for product decisions. Generates interview scripts, survey questionnaires, usability test plans, persona documents, and synthesizes findings into actionable insights. Covers the full research lifecycle from planning to recommendation.

Use when: building a new feature and need validation, running customer discovery, creating user personas, designing surveys, planning usability tests, synthesizing interview notes, or making data-informed product decisions.

## Capabilities
- Research plan generation (objectives, methods, timeline, participant criteria)
- Interview script creation with probing questions
- Survey questionnaire design (quantitative + qualitative)
- Usability test plan with task scenarios
- Persona generation from research data
- Interview/survey response synthesis
- Jobs-to-be-done (JTBD) framework analysis
- Competitive UX benchmarking templates
- Research repository organization

## Commands

### Generate a Research Plan
```
Create a user research plan for [feature/product]
```

Produces a structured plan with:
- Research objectives and hypotheses
- Recommended methodology (interviews, surveys, usability tests, diary studies)
- Participant recruitment criteria and sample size
- Timeline and resource requirements
- Success metrics

### Create Interview Scripts

```
Generate an interview script for [topic/feature]
```

Output includes:
- Warm-up questions (rapport building)
- Core questions organized by theme
- Follow-up probes for each question
- Closing questions and wrap-up
- Interviewer notes and tips

**Best practices baked in:**
- Open-ended questions only (no leading questions)
- "Tell me about..." and "Walk me through..." framing
- Past behavior focus over hypothetical scenarios
- 5 Whys technique for depth
- Time-bounded (45-60 min sessions)

### Design Survey Questionnaires

```
Design a survey for [objective] targeting [audience]
```

Generates:
- Screening questions
- Core questions with appropriate scales (Likert, NPS, SUS, semantic differential)
- Open-ended follow-ups at key points
- Skip logic recommendations
- Estimated completion time
- Platform-agnostic format (works with Typeform, Google Forms, SurveyMonkey)

**Question type selection guide:**

| Goal | Question Type | Scale |
|------|--------------|-------|
| Satisfaction | Likert 5-point | Strongly disagree to Strongly agree |
| Loyalty | NPS | 0-10 |
| Usability | SUS (System Usability Scale) | 10 standard questions |
| Preference | Forced ranking | Rank 1-N |
| Frequency | Multiple choice | Never to Daily |
| Open feedback | Free text | N/A |

### Plan Usability Tests

```
Create a usability test plan for [product/feature]
```

Produces:
- Test objectives and scope
- Participant profile and recruitment criteria
- Task scenarios (3-7 tasks with success criteria)
- Think-aloud protocol instructions
- Metrics to capture (task completion rate, time on task, error rate, SUS score)
- Moderator guide with prompts
- Observer note-taking template
- Post-test questionnaire

**Task scenario format:**
```
Scenario: You want to [realistic context].
Task: [Specific action without revealing UI labels]
Success criteria: [Observable completion state]
Max time: [seconds]
```

### Generate Personas

```
Generate user personas from this research data: [paste notes/data]
```

Creates persona documents with:
- Demographics and context
- Goals and motivations
- Pain points and frustrations
- Current behaviors and workflows
- Technology comfort level
- Quotes from actual research
- Jobs-to-be-done statements
- Design implications

### Synthesize Research Findings

```
Synthesize these interview notes into findings: [paste notes]
```

Analysis includes:
- Affinity mapping of themes
- Frequency analysis (how many participants mentioned each theme)
- Severity/impact ranking
- Key quotes supporting each finding
- Recommendations prioritized by impact and effort
- Confidence level for each finding (based on sample size and consistency)

### JTBD Analysis

```
Analyze jobs-to-be-done for [product/user segment]
```

Generates:
- Job statements in standard format: "When [situation], I want to [motivation], so I can [outcome]"
- Functional, emotional, and social job dimensions
- Outcome expectations with importance and satisfaction ratings
- Opportunity scores (importance + max(importance - satisfaction, 0))
- Underserved needs identification

## Research Method Selection Guide

| Question Type | Best Method | Sample Size | Timeline |
|---------------|------------|-------------|----------|
| "What do users need?" | Discovery interviews | 5-8 | 2 weeks |
| "How do users feel about X?" | Survey | 100+ | 1 week |
| "Can users complete task Y?" | Usability test | 5 per round | 1 week |
| "Which design is better?" | A/B test or preference test | 20+ | 1 week |
| "What do users do naturally?" | Diary study | 10-15 | 2-4 weeks |
| "How does our UX compare?" | Competitive benchmark | 5 per product | 2 weeks |

## Analysis Templates

### Affinity Map Structure
```
Theme: [High-level category]
  Subtheme: [Specific pattern]
    - "Quote from P1" (P1, role)
    - "Quote from P3" (P3, role)
    - Observation: [researcher note]
  Subtheme: [Another pattern]
    - ...
Frequency: X/N participants
Severity: High/Medium/Low
```

### Research Report Template
```markdown
# Research Report: [Study Name]

## Executive Summary
[2-3 sentences: what we studied, key findings, top recommendation]

## Methodology
- Method: [interviews/survey/usability test]
- Participants: [N participants, recruitment criteria]
- Timeline: [dates]

## Key Findings
### Finding 1: [Title]
- **Evidence:** [X/N participants; specific data]
- **Impact:** [How this affects users/business]
- **Recommendation:** [Specific action]

### Finding 2: ...

## Detailed Data
[Tables, charts, full quotes]

## Recommendations
| Priority | Recommendation | Evidence Strength | Effort |
|----------|---------------|-------------------|--------|
| P0 | ... | Strong (8/10 participants) | Medium |
| P1 | ... | Moderate (5/10) | Low |

## Appendix
- Full interview transcripts/survey data
- Participant demographics
- Research instruments used
```

## Tips for Quality Research
1. **Never ask "Would you use this?"** - Ask about past behavior instead
2. **5 interviews reveal 80% of usability issues** (Nielsen/Landauer model)
3. **Surveys validate; interviews discover** - Don't use surveys for discovery
4. **Record sessions** (with consent) - Notes miss nonverbal cues
5. **Separate research from design** - Present findings before solutions
6. **Triangulate** - Use 2+ methods for critical decisions
7. **Report within 48 hours** - Insights decay rapidly

## Guardrails
- All research must include informed consent language
- Never fabricate or embellish participant quotes
- Flag when sample size is too small for statistical significance
- Recommend pilot testing surveys before full launch
- Include limitations section in every research report
