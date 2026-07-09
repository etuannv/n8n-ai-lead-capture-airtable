# n8n AI Lead Capture — Form → Claude AI → Airtable → Telegram

Automatically qualify every form submission using AI — score lead quality (🔥 hot / ⚡ warm / ❄️ cold), extract budget and timeline, recommend next action, log to Airtable, and fire a Telegram alert. All in under 30 seconds.

**Live demo:** [Watch on YouTube](https://youtu.be/hYoz-02DN4Q)  
**Try it now:** [Submit a test inquiry](https://automation.etuannv.com/form/e22e7bc8-9840-416e-bf8d-71f117647172)  
**See results:** [Live Airtable](https://airtable.com/appFVeuyLArweHyTi/shrXQgx46zudpUMCw)  
**Full writeup:** [Blog post](https://etuannv.com/n8n-ai-lead-capture-airtable)  
**Portfolio:** [Upwork case study](https://www.upwork.com/freelancers/~015d7fc44d9ed592a4)

---

## The Problem

Contact forms send an email. That email sits in an inbox. High-value leads wait hours for a response — sometimes losing to a faster competitor.

## The Solution

A 5-node n8n workflow that runs the moment a form is submitted:

```
n8n Form (public URL)
       ↓
Claude AI — qualify + score + recommend next action
       ↓
Parse & structure JSON output
       ↓
Airtable — log all leads with AI scores
       ↓
Telegram — instant alert (🔥 hot / ⚡ warm / ❄️ cold)
```

### Claude AI Output (per submission)

```json
{
  "lead_quality": "hot",
  "project_type": "workflow_automation",
  "budget_usd": 1500,
  "timeline": "urgent",
  "summary": "GrowthCo needs HubSpot-to-Slack automation. Clear scope, realistic budget.",
  "suggested_action": "Schedule discovery call this week — strong fit for a 3-day build."
}
```

### Airtable Schema (12 fields, auto-filled)

| Field | Type | Source |
|---|---|---|
| submitted_at | Date | n8n |
| full_name | Text | Form |
| email | Email | Form |
| company | Text | Form |
| project_description | Long text | Form |
| budget_raw | Text | Form |
| lead_quality | Single select | Claude AI |
| project_type | Single select | Claude AI |
| budget_usd | Number | Claude AI |
| timeline | Single select | Claude AI |
| summary | Long text | Claude AI |
| suggested_action | Long text | Claude AI |

---

## Stack & Cost

| Tool | Purpose | Cost |
|---|---|---|
| n8n (self-hosted) | Workflow engine | ~$5/month VPS |
| Claude Haiku (Anthropic) | AI qualification | <$1/month at 100 leads |
| Airtable | CRM / database | Free tier |
| Telegram Bot | Instant alerts | Free |
| **Total** | | **~$6/month** |

---

## Setup

### Prerequisites
- n8n instance (self-hosted or cloud)
- Anthropic API key (get at console.anthropic.com)
- Airtable account + Personal Access Token
- Telegram bot token (via @BotFather)

### Step 1 — Create Airtable Base

Create a new base named `N8N-Base` with a table named `Leads`.
Add these fields with **exact names** (case-sensitive, snake_case):

```
submitted_at      → Date field
full_name         → Single line text
email             → Email
company           → Single line text
project_description → Long text
budget_raw        → Single line text
lead_quality      → Single select (options: hot, warm, cold)
project_type      → Single select (options: workflow_automation, ai_agent, rag_chatbot, data_pipeline, integration, other)
budget_usd        → Number
timeline          → Single select (options: urgent, normal, flexible)
summary           → Long text
suggested_action  → Long text
```

> ⚠️ Add ALL options to Single Select fields upfront. Airtable will error if Claude returns a value not already in the list.

### Step 2 — Get Airtable Token

1. Go to airtable.com → avatar → **Developer Hub** → **Personal access tokens** → **Create token**
2. Scopes needed:
   - `data.records:write`
   - `schema.bases:read`
   - `schema.bases:write`
3. Access: select your base
4. Copy token (shown only once)

### Step 3 — Import Workflow

1. In n8n: **Workflows → ⋯ → Import from File**
2. Select `workflow/ai-lead-capture.json`
3. Canvas shows 5 nodes connected

### Step 4 — Set Up Credentials

**Anthropic (HTTP Header Auth):**
- Credential type: Header Auth
- Name: `x-api-key`
- Value: your Anthropic API key

**Airtable:**
- Credential type: Airtable Personal Access Token
- Paste your token
- In node "Save to Airtable": select your base and table from the dropdowns

**Telegram:**
- Credential type: Telegram API
- Paste your bot token
- In node "Telegram Alert": replace `REPLACE_TELEGRAM_CHAT_ID` with your chat ID (get from @userinfobot)

### Step 5 — Activate & Test

1. Click **Publish** (top right toggle)
2. Copy the **Production URL** from the Form Trigger node
3. Open the URL and submit a test inquiry
4. Within 30 seconds: check Airtable for new row + Telegram for alert

---

## File Structure

```
n8n-ai-lead-capture-airtable/
├── workflow/
│   └── ai-lead-capture.json    # Import this into n8n
├── .env.example                # Environment variable template
├── .gitignore
└── README.md
```

---

## Related Project

→ [n8n-ai-email-triage](https://github.com/etuannv/n8n-ai-email-triage) — Same AI pattern applied to Gmail inbox: classify emails as hot_lead/recruiter/spam with priority scoring and reply drafting.

---

## Blog Posts
**📖 Read the prompt engineering writeup:** https://etuannv.com/prompt-engineering-5-levels  

**📖 Read the Claude Code, Efficiently writeup:** https://etuannv.com/claude-code-efficiently  


## Author

**Tuan Nguyen** — AI Automation & n8n Developer  
Top Rated Plus on Upwork · 100% Job Success · 4,000+ hours

→ [Upwork](https://www.upwork.com/freelancers/etuannv) · [Freelancer](https://www.freelancer.com/u/etuannv) · [Blog](https://etuannv.com)

---

## License

MIT — free to use and adapt. If this saved you time, a ⭐ on the repo is appreciated.
