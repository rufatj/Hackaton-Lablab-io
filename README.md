# IntelScout AI
### Real-Time Competitive Intelligence Platform

Built at the Bright Data Hackathon · Powered by n8n + Bright Data SERP API + Gemini AI

---

## What It Does

IntelScout AI lets you type a company name and a target market, then delivers
a structured GTM intelligence report in under 30 seconds — pulled from live
web data, not static databases.

The output includes a Sentiment Score, categorized Opportunities and Risk
Signals, and a full narrative report with sourced bullet points.

---

## Architecture
User (HTML Interface)
│
▼ POST /webhook
n8n Webhook Node
│
▼
Validation & Edit Fields
│
▼
Bright Data SERP API  ←── Live Google search results (2 / 10 / 30 pages)
│
▼
Gemini AI Agent  ←── Structured prompt → JSON output
│
▼
Code Node (JSON parse + normalize)
│
▼
Respond to Webhook  ──► HTML Dashboard renders

---

## Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML + CSS + Chart.js |
| Workflow | n8n (self-hosted) |
| Live Data | Bright Data SERP API |
| AI | Google Gemini via n8n AI Agent node |
| Hosting | GitHub Pages (frontend) + VPS (n8n) |

---

## Frontend Setup

1. Clone this repo
2. Open `index.html`
3. Find this line and paste your n8n webhook URL:
```javascript
const WEBHOOK_URL = "YOUR_WEBHOOK_URL_HERE";
```
4. Open the file in any browser — no build step, no dependencies

---

## n8n Workflow Setup

1. In n8n, create a new workflow
2. Import `workflow.json` from this repo (File → Import from file)
3. Add your credentials:
   - **Bright Data**: HTTP Request node → Header Auth →
     `Authorization: Bearer YOUR_BRIGHT_DATA_API_KEY`
   - **Gemini**: AI Agent node → Google Gemini credential →
     paste your API key from aistudio.google.com
4. Set Webhook node → Response Mode → `First Incoming Item's Data`
5. Activate the workflow
6. Copy the webhook URL into `index.html`

---

## n8n Workflow Node Order
Webhook → Validation Code → Edit Fields →
HTTP Request (Bright Data) → AI Agent (Gemini) →
Code (JSON parse) → Respond to Webhook

---

## Bright Data Configuration

- **Endpoint:** `https://api.brightdata.com/request`
- **Zone:** your SERP zone name (e.g. `serp_api1rufat`)
- **Method:** POST
- **Body:**
```json
{
  "zone": "YOUR_ZONE_NAME",
  "url": "https://www.google.com/search?q=COMPANY+MARKET&num=10",
  "format": "json"
}
```

---

## AI Agent Prompt Structure

The Gemini agent receives:
- Company name, target market, industry, focus areas
- Raw Bright Data SERP results (organic search data)
- User's strategic context and questions

It returns a strict JSON object:
```json
{
  "report": "Full narrative report text",
  "sentiment": 72,
  "opportunities": ["item 1", "item 2"],
  "risks": ["item 1", "item 2"],
  "signals_count": 14
}
```

