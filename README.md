### Agent Descriptions

**Person Research Agent**
Takes the LinkedIn URL and Apollo profile data. Extracts role-relevant context about the individual, their professional priorities, and the pressures typical to their position. Returns a structured JSON summary.

**Company Research Agent**
Takes the company name and researches public web data. Extracts industry vertical, company size, market positioning, and recent developments. Returns a structured JSON summary.

**Mission and Solutions Agent (Maxicus Solutions)**
Receives the person and company summaries. Reasons about fit between the prospect's context and the sender's offering. Returns 2 to 3 prioritized outreach angles.

**Email Draft Agents (x3)**
Three separate drafting agents write the primary outreach email and two follow-up emails. Each draft references the research summaries and outreach angles from prior stages. No generic openers or template placeholders are used.

---

## External Integrations

| Service | Purpose | Auth Required |
|---------|---------|---------------|
| Apollo.io API | Lead enrichment (name, email, title, company) | API Key |
| OpenAI API (GPT-4o-mini) | All AI agent and drafting nodes | API Key |
| Lemlist API | Campaign creation, sequence steps, lead creation | API Key |
| Google Sheets API | Lead logging and intermediate data storage | OAuth2 / Service Account |

---

## Trigger Modes

The workflow supports two ways to start a run:

1. **Webhook Mode** - Send a POST request to the n8n webhook URL with a JSON body containing the lead's LinkedIn URL. The ABM Brain processes it immediately.

2. **Batch Mode** - A parent workflow iterates over a list of leads exported from Apollo into Google Sheets and calls this workflow for each lead using the "When Executed by Another Workflow" trigger.

---

## Key Design Decisions

- Each agent has a tightly scoped role and returns structured output consumed by the next stage.
- The human review step in Lemlist is mandatory before any campaign activates. No email is sent without reviewer approval.
- Prompt versioning is treated like code. Any prompt change was tested against a fixed benchmark set before deployment.
- Hallucination mitigation is handled at two levels: a flagging instruction in the Company Research prompt, and the human review gate in Lemlist.

---

## Repository Contents
