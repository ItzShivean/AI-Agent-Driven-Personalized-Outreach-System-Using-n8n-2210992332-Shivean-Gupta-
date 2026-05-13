# AI-Agent Driven Personalized Outreach System Using n8n

**Project:** Team 6
**Author:** Shivean Gupta
**Roll Number:** 2210992332
**Email:** shivean2332.be22@chitkara.edu.in
**University:** Chitkara University, Rajpura, Punjab, India

---

## Overview

This repository contains the n8n workflow source file for the **ABM AskMax** personalized outreach pipeline. The system uses a coordinated set of AI agents to automate B2B cold email research, drafting, and delivery. One LinkedIn URL and a lead record go in. A fully researched, personalized three-email sequence comes out, ready to send via Lemlist.

The workflow was built as part of a research project exploring how multi-agent AI systems can replace manual email personalization at scale. It was deployed across 300 leads and achieved a 42.3% open rate, outperforming the industry benchmark for personalized cold outreach.

---

## Repository Contents
├── IPR Submission File/
│   └── Proof of submission (screenshot/image)
│
├── PPT and Report/
│   ├── Team_6_Project_PPT.pptx        # Final project presentation
│   └── Team_6_Projec_Report.docx      # Final detailed project report
│
└── Source Code/
└── N8N-Workflow.json               # Complete n8n workflow (21 nodes, JSON export)

---

## Workflow Architecture

The workflow is named **ABM AskMax** and is organized into two layers.

### Coordination Layer

| Node | Type | Role |
|------|------|------|
| Webhook | Trigger | Receives incoming lead data via HTTP POST |
| When Executed by Another Workflow | Trigger | Entry point for batch processing mode |
| ABM BRAIN | AI Agent | Coordinates task routing; powered by GPT-4o-mini |
| Simple Memory | Memory Buffer | Maintains context window across agent steps |
| ABM | Tool Workflow | Sub-workflow tool available to the ABM Brain |
| Respond to Webhook | Output | Returns confirmation response to webhook caller |

### Execution Layer (Sequential Pipeline)
Apollo Enrichment (HTTP Request)
|
v
Append or Update Row in Google Sheets
|
v
Person Research Agent (OpenAI)
|
v
Company Research Agent (OpenAI)
|
v
Mission and Solutions Agent (OpenAI)
|
v
Email Draft Agent - Primary Email (OpenAI)
|
v
Email Draft Agent - Follow-up 1 (OpenAI)
|
v
Email Draft Agent - Follow-up 2 (OpenAI)
|
v
Create Lemlist Campaign + Sequence Steps
|
v
Create Lead in Campaign (Lemlist)

### Agent Descriptions

**Person Research Agent** - Takes the LinkedIn URL and Apollo profile data. Extracts role-relevant context, professional priorities, and pressures typical to the position. Returns a structured JSON summary.

**Company Research Agent** - Takes the company name and researches public web data. Extracts industry vertical, company size, market positioning, and recent developments. Returns a structured JSON summary.

**Mission and Solutions Agent** - Receives the person and company summaries. Reasons about fit between the prospect's context and the sender's offering. Returns 2 to 3 prioritized outreach angles.

**Email Draft Agents (x3)** - Three separate drafting agents write the primary outreach email and two follow-up emails. Each draft references the research summaries and outreach angles. No generic openers or template placeholders are used.

---

## External Integrations

| Service | Purpose | Auth Required |
|---------|---------|---------------|
| Apollo.io API | Lead enrichment (name, email, title, company) | API Key |
| OpenAI API (GPT-4o-mini) | All AI agent and drafting nodes | API Key |
| Lemlist API | Campaign creation, sequence steps, lead creation | API Key |
| Google Sheets API | Lead logging and intermediate data storage | OAuth2 / Service Account |

---

## Installation and Setup

To get this workflow running, install n8n either via npm (`npm install -g n8n` then `n8n start`) or use n8n Cloud at https://n8n.io. Once n8n is running, open the editor, go to **Settings > Import Workflow**, and upload the `N8N-Workflow.json` file from the Source Code folder. After importing, you will need to configure credentials for OpenAI, Apollo.io, Lemlist, and Google Sheets under **Settings > Credentials**. With credentials in place, the workflow is ready to accept leads via webhook or batch mode.

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

## License

This project was developed for academic purposes at Chitkara University. All rights reserved by the author.
