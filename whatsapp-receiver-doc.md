Your WhatsApp Receiver — Portfolio Doc (Draft)
What this workflow does:
Receives incoming WhatsApp messages from customers, parses the raw data, generates an AI reply, and logs the conversation to a database.
How data flows (step by step):

Webhook node — Meta sends a POST request to n8n every time a customer messages the WhatsApp number. This is the entry point.
JS Parse node — The raw Meta payload is messy JSON. This node extracts three clean fields: phone, name, and body (the message text).
Claude Agent node — Takes the message body and generates a plain-text reply. This is the AI brain of the workflow.
Supabase node — Logs the conversation (phone, name, message, reply) to the database. Also checks if this user already exists before inserting.
WhatsApp API node — Sends the generated reply back to the customer's phone number via the Meta Cloud API.

Where it could fail:

Meta sends a malformed payload → JS parse node breaks, fields come out null (you already hit this with the phone field)
Supabase is down → log step fails silently, reply still sends but nothing is recorded
Claude API times out → no reply generated, customer gets nothing
Webhook collision after a restart → production traffic goes to wrong workflow (you already debugged this)
