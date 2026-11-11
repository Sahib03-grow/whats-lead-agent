# WhatsApp Lead Agent (n8n + Claude)

An AI assistant for small businesses (gyms, clinics, salons, coaching classes) that answers WhatsApp enquiries, qualifies leads, saves them to Google Sheets and alerts the owner on Telegram.

> Built with [n8n](https://n8n.io), the WhatsApp Business Cloud API and the Claude API.

## What it does
- Replies to customers on WhatsApp in their language (Hindi, Hinglish or English)
- Answers questions only from the business info you provide (services, prices, timings, address)
- Qualifies leads one question at a time: need, budget, preferred time, area
- Saves and updates each lead in Google Sheets (one row per phone number)
- Sends the owner a Telegram alert for hot leads, complaints and anything it cannot answer
- **Approval mode (default ON):** drafts go to the owner on Telegram instead of being sent automatically

## How it works
```
WhatsApp message -> n8n webhook -> Claude -> safety check
                                      |-> Google Sheet (lead)
                                      |-> Telegram (hot lead / escalation / draft)
                                      '-> WhatsApp reply (when approval mode is OFF)
```

## Requirements
- n8n (Cloud or self-hosted with a public HTTPS URL)
- Meta developer account with the WhatsApp Business Cloud API
- Claude API key
- Telegram bot (for owner alerts) and a Google Sheet

## Quick start
1. **Import** `n8n/whatsapp_lead_agent.json` into n8n.
2. **Credentials:** create a Header Auth credential (`x-api-key` = your Claude API key), a Telegram credential and a Google Sheets credential. Select them in the Claude, Telegram and Save Lead nodes.
3. **Config node:** fill `phone_number_id`, `wa_token` and `owner_chat_id`. Keep `approval_mode` = true at first.
4. **Business info:** open the `Build Claude Request` node and edit the `BIZ` block at the top (name, services and prices, timings, address, booking link).
5. **Google Sheet:** create a tab named `Leads` with the header row from `leads_sheet_template.csv`.
6. **Webhook:** activate the workflow. In Meta for Developers, WhatsApp > Configuration, set the callback URL to `https://<your-n8n>/webhook/wa-leads` and the verify token to `change-this-verify-token` (change it in the `Return Challenge` node first). Subscribe to the `messages` field.
7. **Test** from a number added as a test recipient, check the Telegram drafts, then decide when to turn approval mode off.

## Safety and compliance
- WhatsApp allows free-form replies only within 24 hours of the customer's last message. Messages outside that window need approved templates (not included yet).
- Only message people who contacted you first or opted in. No bulk or spam messaging, it gets numbers banned.
- The agent does not give medical, legal or financial advice and never guesses prices. Those cases are escalated to the owner.
- Never commit API keys or tokens. Keep them in n8n credentials and the Config node only on your own instance.

## Limitations
- Conversation memory and lead merging use n8n workflow static data, which works only while the workflow is active and is lost if the workflow is re-imported.
- Text messages only. Images, voice notes and documents are flagged and the customer is asked to type.
- Escalated conversations are not auto-replied. The owner answers manually.

## Roadmap
- Telegram approve / reject buttons
- Follow-up reminders inside the 24-hour window
- Appointment booking with Google Calendar
- Template messages for follow-ups after 24 hours

## License
MIT. See [LICENSE](LICENSE).
