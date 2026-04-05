---
name: twenty-crm
slug: twenty-crm
description: Read and write contacts, companies, deals, notes, and tasks in the Twenty CRM. Use for lead management, pipeline tracking, and CRM operations.
metadata: {"clawdbot":{"emoji":"📇","os":["linux","darwin","win32"]}}
---

# Twenty CRM Bridge

Read and write contacts, companies, deals, notes, and tasks in Twenty CRM via GraphQL.

## Config

- **Endpoint**: `http://localhost:3001/graphql`
- **Auth**: `Authorization: Bearer <TWENTY_API_KEY>`
- **API Key**: stored in `~/.openclaw/workspace/twenty/.env` as `TWENTY_API_KEY`

When making requests, always set headers:
```
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI3NTgyNDZhZC0zM2VjLTRjMDQtYjYzMy0wZDMxNGZjZjU3ZWMiLCJ0eXBlIjoiQVBJX0tFWSIsIndvcmtzcGFjZUlkIjoiNzU4MjQ2YWQtMzNlYy00YzA0LWI2MzMtMGQzMTRmY2Y1N2VjIiwiaWF0IjoxNzc0NjY0NTAzLCJleHAiOjQ5MjgyNjgxMDIsImp0aSI6IjIzYjhjOTJkLTg2ZmQtNDJlMi1iZWIxLWFmZTUxYWM1MWRkMCJ9.sTz-zMqe5O0ShU1iJZpqVGg4H7Sac1Eer-cVEQXFxc4
```

---

## Reading Data

### List People (Contacts)

```graphql
{
  people(filter: {}, orderBy: { createdAt: DescNullsLast }, first: 20) {
    edges {
      node {
        id
        name { firstName lastName }
        emails { primaryEmail }
        phones { primaryPhoneNumber }
        city
        jobTitle
        linkedinLink { primaryLinkUrl }
        createdAt
        updatedAt
      }
    }
  }
}
```

### Get Person by ID

```graphql
{
  people(filter: { id: { eq: "PERSON_ID" } }) {
    edges {
      node {
        id
        name { firstName lastName }
        emails { primaryEmail }
        phones { primaryPhoneNumber }
        city
        jobTitle
        linkedinLink { primaryLinkUrl }
        company { id name }
        opportunities { edges { node { id name stage } } }
      }
    }
  }
}
```

### Search People by Name

```graphql
{
  people(
    filter: {
      or: [
        { name: { firstName: { like: "%SEARCH%" } } }
        { name: { lastName: { like: "%SEARCH%" } } }
      ]
    }
  ) {
    edges {
      node {
        id
        name { firstName lastName }
        emails { primaryEmail }
        jobTitle
        company { name }
      }
    }
  }
}
```

### List Companies

```graphql
{
  companies(orderBy: { createdAt: DescNullsLast }, first: 20) {
    edges {
      node {
        id
        name
        domainName { primaryLinkUrl }
        employees
        annualRecurringRevenue { amountMicros currencyCode }
        linkedinLink { primaryLinkUrl }
        techStack
        icpScore
        fundingStage
        enrichedAt
        createdAt
      }
    }
  }
}
```

### List Opportunities (Deals)

```graphql
{
  opportunities(orderBy: { createdAt: DescNullsLast }, first: 20) {
    edges {
      node {
        id
        name
        stage
        outreachStatus
        product
        amount { amountMicros currencyCode }
        closeDate
        probability
        pointOfContact { name { firstName lastName } emails { primaryEmail } }
        company { id name icpScore techStack fundingStage employees }
        createdAt
      }
    }
  }
}
```

**Opportunity stages**: `NEW` | `SCREENING` | `MEETING` | `PROPOSAL` | `CUSTOMER` | `LOST`

**Outreach status** (custom field): `NEW_LEAD` | `CONTACTED` | `REPLIED` | `QUALIFIED` | `DISQUALIFIED`

**Product** (custom field): `KERFOS` | `GAPHUNTER` | `CLAWCOST`

### List Notes

```graphql
{
  notes(orderBy: { createdAt: DescNullsLast }, first: 20) {
    edges {
      node {
        id
        title
        body
        createdAt
        noteTargets {
          edges {
            node {
              person { name { firstName lastName } }
              company { name }
              opportunity { name }
            }
          }
        }
      }
    }
  }
}
```

### List Tasks

```graphql
{
  tasks(orderBy: { dueAt: AscNullsLast }, first: 20) {
    edges {
      node {
        id
        title
        body
        status
        dueAt
        assignee { name { firstName lastName } }
        taskTargets {
          edges {
            node {
              person { name { firstName lastName } }
              company { name }
              opportunity { name }
            }
          }
        }
      }
    }
  }
}
```

**Task statuses**: `TODO` | `IN_PROGRESS` | `DONE`

---

## Writing Data

### Create Person

```graphql
mutation {
  createPerson(data: {
    name: { firstName: "Jane", lastName: "Smith" }
    emails: { primaryEmail: "jane@example.com" }
    phones: { primaryPhoneNumber: "+1-555-0100" }
    jobTitle: "CEO"
    city: "New York"
    linkedinLink: { primaryLinkUrl: "https://linkedin.com/in/janesmith" }
  }) {
    id
    name { firstName lastName }
    createdAt
  }
}
```

### Update Person

```graphql
mutation {
  updatePerson(
    id: "PERSON_ID"
    data: {
      jobTitle: "Founder"
      city: "San Francisco"
    }
  ) {
    id
    name { firstName lastName }
    jobTitle
    updatedAt
  }
}
```

### Create Company

```graphql
mutation {
  createCompany(data: {
    name: "Acme Corp"
    domainName: { primaryLinkUrl: "acme.com" }
    employees: 50
    linkedinLink: { primaryLinkUrl: "https://linkedin.com/company/acme" }
  }) {
    id
    name
    createdAt
  }
}
```

### Create Opportunity (Deal)

```graphql
mutation {
  createOpportunity(data: {
    name: "Enterprise Deal"
    stage: NEW
    amount: { amountMicros: 50000000000, currencyCode: "USD" }
    closeDate: "2026-06-30"
    probability: 20
  }) {
    id
    name
    stage
    amount { amountMicros currencyCode }
    createdAt
  }
}
```

Note: `amountMicros` = dollar amount × 1,000,000 (e.g. $50,000 = 50000000000)

### Update Opportunity Stage

```graphql
mutation {
  updateOpportunity(
    id: "OPPORTUNITY_ID"
    data: { stage: PROPOSAL, probability: 60 }
  ) {
    id
    name
    stage
    probability
    updatedAt
  }
}
```

### Create Note

```graphql
mutation {
  createNote(data: {
    title: "Call summary"
    body: "Discussed pricing and timeline. Interested in Enterprise plan."
  }) {
    id
    title
    createdAt
  }
}
```

### Attach Note to Contact/Company/Deal

After creating a note, attach it with `createNoteTarget`:

```graphql
mutation {
  createNoteTarget(data: {
    noteId: "NOTE_ID"
    personId: "PERSON_ID"
  }) {
    id
  }
}
```

Replace `personId` with `companyId` or `opportunityId` as needed.

### Create Task

```graphql
mutation {
  createTask(data: {
    title: "Follow up on proposal"
    body: "Send revised pricing doc"
    status: TODO
    dueAt: "2026-04-05T09:00:00Z"
  }) {
    id
    title
    status
    dueAt
    createdAt
  }
}
```

### Complete Task

```graphql
mutation {
  updateTask(id: "TASK_ID", data: { status: DONE }) {
    id
    title
    status
    updatedAt
  }
}
```

---

## Common Workflows

### Add a New Lead
1. Create company (if not exists): `createCompany`
2. Create person: `createPerson`
3. Create opportunity: `createOpportunity`
4. Link person to company via `updatePerson(data: { companyId: "..." })`
5. Add intake note: `createNote` then `createNoteTarget`

### Log a Sales Call
1. Find person by name or email using search query
2. Create note with call summary
3. Attach note to person + opportunity
4. Create follow-up task with due date

### Move Deal Through Pipeline
1. Query opportunity by name or ID
2. Update stage: `NEW` → `SCREENING` → `MEETING` → `PROPOSAL` → `CUSTOMER`
3. Update probability as deal progresses
4. Add note documenting the stage change

### Find All Deals for a Company
```graphql
{
  opportunities(filter: { company: { id: { eq: "COMPANY_ID" } } }) {
    edges {
      node { id name stage amount { amountMicros currencyCode } }
    }
  }
}
```

---

## How to Execute Queries

Use `curl` or any HTTP tool:

```bash
curl -s http://localhost:3001/graphql \
  -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI3NTgyNDZhZC0zM2VjLTRjMDQtYjYzMy0wZDMxNGZjZjU3ZWMiLCJ0eXBlIjoiQVBJX0tFWSIsIndvcmtzcGFjZUlkIjoiNzU4MjQ2YWQtMzNlYy00YzA0LWI2MzMtMGQzMTRmY2Y1N2VjIiwiaWF0IjoxNzc0NjY0NTAzLCJleHAiOjQ5MjgyNjgxMDIsImp0aSI6IjIzYjhjOTJkLTg2ZmQtNDJlMi1iZWIxLWFmZTUxYWM1MWRkMCJ9.sTz-zMqe5O0ShU1iJZpqVGg4H7Sac1Eer-cVEQXFxc4" \
  -d '{"query": "{ people { edges { node { id name { firstName lastName } } } } }"}'
```

Always parse the JSON response and check for `errors` before using `data`.
