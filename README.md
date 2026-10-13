# AI Complaint Router (Make.com)

An automation built in Make.com that receives customer complaints through a webhook, uses an AI model to classify them, and routes each one to the right Google Sheets tab: **Urgent**, **Billing**, or **General**.

## What it does

1. **Webhook** receives a complaint as JSON (`message`).
2. A **filter** blocks empty or invalid input so the AI step never runs on bad data.
3. **HTTP module** calls the OpenRouter chat completions API (`/api/v1/chat/completions`) to classify the complaint.
4. **Parse JSON** turns the AI response into usable fields.
5. **Router** sends the complaint down one of three routes:
   - **Urgent** → Google Sheets "Urgent" tab
   - **Billing** → Google Sheets "Billing" tab
   - **General** (fallback) → Google Sheets "General" tab
6. **Error handling:** if the AI call fails, a **Gmail** module sends an alert email, then a **Skip** step ends that run cleanly so one bad request doesn't leave the scenario stuck.

## Flow

```
Webhook → [Message present?] → HTTP (OpenRouter) → Parse JSON → Router
                                      │                          ├─ Urgent  → Google Sheets
                                      └─ on error → Gmail → Skip ├─ Billing → Google Sheets
                                                                 └─ General → Google Sheets
```

## Tools used

- Make.com (Webhooks, HTTP, JSON, Router, Google Sheets, Gmail)
- OpenRouter API (LLM classification)
- Google Sheets

## Example request

```json
{
  "message": "I was charged twice for my invoice this month."
}
```

Expected result: the complaint is classified as **Billing** and a new row is added to the Billing tab.

## Setup

1. Import `blueprint.json` into Make.com (Create a new scenario → three dots → Import Blueprint).
2. Reconnect your own Google Sheets and Gmail connections.
3. Add your own OpenRouter API key in the HTTP module headers.
4. Create a Google Sheet with three tabs: Urgent, Billing, General, and map the columns in each Google Sheets module.
5. Copy the webhook URL and send a test POST request.

> Do not commit real API keys. The blueprint in this repo has keys removed.

## Testing

- Urgent, Billing and General messages each land in the correct tab.
- An empty or missing `message` is stopped by the filter.
- A failed AI call triggers the Gmail alert and then skips cleanly.

## Screenshots

Add scenario and Google Sheets screenshots in a `/screenshots` folder and link them here.

## Author

Hope Mutheu
