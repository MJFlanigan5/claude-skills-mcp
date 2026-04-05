---
name: mermaid-diagrams
description: Comprehensive guide for creating software diagrams using Mermaid syntax. Use when users need to create, visualize, or document software through diagrams including class diagrams, sequence diagrams, flowcharts, ERDs, C4 architecture diagrams, state diagrams, git graphs, pie charts, gantt charts, or any other diagram type. Triggers on requests to "diagram", "visualize", "model", "map out", "show the flow", or when explaining system architecture, database design, code structure, or user/application flows.
---

# Mermaid Diagramming Skill

Create professional software diagrams using Mermaid's text-based syntax. Mermaid renders diagrams from simple text definitions, making them version-controllable, easy to update, and maintainable alongside code.

## Why This Skill

Every agent that writes code, documents systems, or explains architecture needs diagrams. This skill gives you:

- **9 diagram types** with syntax reference and examples
- **Selection guide** — pick the right diagram for the job
- **Best practices** — avoid common pitfalls that break renders
- **Theming & export** — configure look, export to PNG/SVG
- **Production patterns** — real-world examples for APIs, databases, CI/CD, domain models

No dependencies. Works anywhere Mermaid renders (GitHub, VS Code, Notion, Obsidian, Confluence, or CLI export).

## Diagram Type Selection Guide

| Need | Diagram Type |
|------|-------------|
| Domain modeling, OOP design | Class Diagram |
| API flows, message passing | Sequence Diagram |
| Processes, algorithms, decisions | Flowchart |
| Database schemas | ERD |
| System architecture (multi-level) | C4 Diagram |
| State machines, lifecycles | State Diagram |
| Branching strategies | Git Graph |
| Project timelines | Gantt Chart |
| Data breakdowns | Pie/Bar Chart |

## Core Syntax

All Mermaid diagrams follow this pattern:

```mermaid
diagramType
  definition content
```

**Key rules:**
- First line declares diagram type (`classDiagram`, `sequenceDiagram`, `flowchart`, etc.)
- Use `%%` for comments
- Line breaks and indentation improve readability but aren't required
- Unknown keywords break diagrams silently — validate syntax before shipping

## Quick Start Examples

### Class Diagram (Domain Model)
```mermaid
classDiagram
    Title -- Genre
    Title *-- Season
    Title *-- Review
    User --> Review : creates

    class Title {
        +string name
        +int releaseYear
        +play()
    }

    class Genre {
        +string name
        +getTopTitles()
    }
```

### Sequence Diagram (API Flow)
```mermaid
sequenceDiagram
    participant User
    participant API
    participant Database

    User->>API: POST /login
    API->>Database: Query credentials
    Database-->>API: Return user data
    alt Valid credentials
        API-->>User: 200 OK + JWT token
    else Invalid credentials
        API-->>User: 401 Unauthorized
    end
```

### Flowchart (User Journey)
```mermaid
flowchart TD
    Start([User visits site]) --> Auth{Authenticated?}
    Auth -->|No| Login[Show login page]
    Auth -->|Yes| Dashboard[Show dashboard]
    Login --> Creds[Enter credentials]
    Creds --> Validate{Valid?}
    Validate -->|Yes| Dashboard
    Validate -->|No| Error[Show error]
    Error --> Login
```

### ERD (Database Schema)
```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
    PRODUCT ||--o{ LINE_ITEM : includes

    USER {
        int id PK
        string email UK
        string name
        datetime created_at
    }

    ORDER {
        int id PK
        int user_id FK
        decimal total
        datetime created_at
    }
```

### C4 System Context
```mermaid
C4Context
    title System Context - Online Store

    Person(customer, "Customer", "Browses and buys products")
    System(store, "Online Store", "Main e-commerce platform")
    System_Ext(payment, "Payment Gateway", "Processes payments")
    System_Ext(shipping, "Shipping API", "Handles delivery")

    Rel(customer, store, "Uses", "HTTPS")
    Rel(store, payment, "Processes payments", "API")
    Rel(store, shipping, "Ships orders", "API")
```

### State Diagram
```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Review : submit
    Review --> Approved : approve
    Review --> Draft : request changes
    Approved --> Published : publish
    Published --> Archived : archive
    Archived --> [*]
```

### Gantt Chart
```mermaid
gantt
    title Project Timeline
    dateFormat YYYY-MM-DD
    section Design
        Wireframes     :a1, 2024-01-01, 7d
        Mockups        :a2, after a1, 5d
    section Development
        Frontend       :b1, after a2, 14d
        Backend        :b2, after a2, 14d
    section Launch
        Testing        :c1, after b1, 7d
        Deploy         :c2, after c1, 2d
```

## Configuration and Theming

Configure diagrams using frontmatter:

```mermaid
---
config:
  theme: base
  themeVariables:
    primaryColor: "#ff6b6b"
---
flowchart LR
    A --> B
```

**Available themes:** `default`, `forest`, `dark`, `neutral`, `base`

**Look options:**
- `look: classic` — Traditional Mermaid style
- `look: handDrawn` — Sketch-like appearance

## Exporting and Rendering

**Native support:** GitHub, GitLab, VS Code (with extension), Notion, Obsidian, Confluence

**CLI export:**
```bash
npm install -g @mermaid-js/mermaid-cli
mmdc -i input.mmd -o output.png
mmdc -i input.mmd -o output.svg
```

**Docker:**
```bash
docker run --rm -v $(pwd):/data minlag/mermaid-cli -i /data/input.mmd -o /data/output.png
```

**Online:** [mermaid.live](https://mermaid.live) — browser editor with PNG/SVG export

## Best Practices

1. **Start simple** — core entities first, add detail incrementally
2. **One concept per diagram** — split complex systems into focused views
3. **Use meaningful names** — clear labels make diagrams self-documenting
4. **Comment with `%%`** — explain complex relationships
5. **Version control** — store `.mmd` files alongside code
6. **Validate before shipping** — paste into mermaid.live to catch syntax errors

## Common Pitfalls

- **Special characters** — `{}` in text nodes breaks rendering; wrap in quotes
- **Misspellings** — keyword typos fail silently
- **Overcomplexity** — diagrams with 20+ nodes become unreadable; decompose
- **Missing relationships** — if two things interact, draw the line
