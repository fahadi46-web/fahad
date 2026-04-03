# ChatGPT Bot → GoHighLevel Integration (n8n)

This n8n workflow automatically summarizes a GoHighLevel contact using ChatGPT and saves the summary as a note on that contact.

## How It Works

```
GoHighLevel Webhook  →  ChatGPT (summarize contact)  →  Save as Note in GHL
```

1. GoHighLevel fires a webhook when a contact is created or updated
2. The workflow sends the contact's data to ChatGPT (GPT-4o-mini)
3. ChatGPT returns a 2–3 sentence summary of the contact
4. The summary is saved as a timestamped note on the GHL contact

---

## Setup

### Step 1 — Import the Workflow into n8n

1. Open your n8n instance
2. Go to **Workflows → Import from File**
3. Upload `n8n-workflow.json`

---

### Step 2 — Create Credentials in n8n

You need two HTTP Header Auth credentials.

#### A. OpenAI API Key

1. In n8n go to **Credentials → New → HTTP Header Auth**
2. Set:
   - **Name:** `OpenAI API Key`
   - **Header Name:** `Authorization`
   - **Header Value:** `Bearer sk-YOUR_OPENAI_KEY_HERE`
3. Save and assign it to the **"ChatGPT - Summarize Contact"** node

> Get your OpenAI key at: https://platform.openai.com/api-keys

#### B. GoHighLevel API Key

1. In n8n go to **Credentials → New → HTTP Header Auth**
2. Set:
   - **Name:** `GoHighLevel API Key`
   - **Header Name:** `Authorization`
   - **Header Value:** `Bearer YOUR_GHL_API_KEY_HERE`
3. Save and assign it to the **"Add Note to GHL Contact"** node

> Get your GHL API key:
> GHL → Settings → Integrations → API Key

---

### Step 3 — Get Your Webhook URL

1. Open the imported workflow in n8n
2. Click the **"GoHighLevel Webhook"** node
3. Copy the **Webhook URL** shown (looks like `https://your-n8n.com/webhook/ghl-contact-summary`)

---

### Step 4 — Configure GoHighLevel Webhook

1. In GHL go to **Settings → Integrations → Webhooks**
2. Click **Add Webhook**
3. Set:
   - **Webhook URL:** paste the URL from Step 3
   - **Events:** Select `Contact Created` and/or `Contact Updated`
4. Save

---

### Step 5 — Activate the Workflow

1. In n8n, open the workflow
2. Toggle **Active** to ON (top-right switch)

---

## Testing

To test without waiting for a real GHL event:

1. In n8n, open the workflow
2. Click **"GoHighLevel Webhook"** node → **Listen for Test Event**
3. In GHL, create or update a contact
4. n8n will capture the payload and you can run through the workflow manually

Or use the **Test Webhook URL** (shown when in test mode) and send a POST request with sample data:

```bash
curl -X POST https://your-n8n.com/webhook-test/ghl-contact-summary \
  -H "Content-Type: application/json" \
  -d '{
    "body": {
      "id": "CONTACT_ID_HERE",
      "firstName": "Jane",
      "lastName": "Doe",
      "email": "jane@example.com",
      "phone": "+15551234567",
      "companyName": "Acme Corp",
      "tags": ["hot-lead", "webinar"],
      "city": "Austin",
      "state": "TX",
      "country": "US",
      "source": "Facebook Ad"
    }
  }'
```

---

## Customizing the ChatGPT Prompt

Open the **"ChatGPT - Summarize Contact"** node and edit the `jsonBody` field.

The system prompt is:
> "You are a CRM assistant. Summarize the contact information provided in 2-3 concise sentences..."

You can change it to anything — e.g., score the lead, detect intent, suggest next steps, etc.

---

## GHL Webhook Payload Fields

The workflow reads these fields from the GHL webhook body:

| Field | Description |
|-------|-------------|
| `id` | Contact ID (used to save the note) |
| `firstName` / `lastName` | Contact name |
| `email` | Email address |
| `phone` | Phone number |
| `companyName` | Company |
| `tags` | Array of tags |
| `city`, `state`, `country` | Location |
| `source` | Lead source |
| `type` | Contact type |

If your GHL webhook sends the contact ID under a different key (e.g., `contactId`), update the **"Add Note to GHL Contact"** node URL accordingly:

```
https://rest.gohighlevel.com/v1/contacts/{{ $('GoHighLevel Webhook').first().json.body.contactId }}/notes/
```

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Note not saved | Check the GHL API key has write permissions |
| ChatGPT not responding | Verify OpenAI API key and account has credits |
| Webhook not firing | Make sure the workflow is **Active** and GHL webhook URL is correct |
| `id` field missing | Check GHL webhook payload structure in n8n execution logs |
