# JS Snippets — DanKai Media

A personal library of JavaScript snippets used in n8n Code nodes.
For each one: what it does, where it's used, and one line I can change.

---

## Snippet 001 — Parse WhatsApp Payload

**Used in:** whatsapp_receiver_v1 → Parse WhatsApp Payload node
**What it does:** Takes Meta's raw nested JSON and extracts three clean
fields (phone, name, message) that the rest of the workflow needs.

**Key patterns learned:**
- `??` — fallback if null/undefined ("use this OR that")
- `?.` — optional chaining, don't crash if field is missing
- `|| 'default'` — fallback to a default value
- `return [{ json: {...} }]` — how n8n Code nodes must return data

**The line I'd change if Meta's schema changes:**
```javascript
const phone = change.contacts?.[0]?.wa_id 
           || change.messages?.[0]?.from 
           || '';
```
If Meta moves the phone to a new location, update the path here.

---