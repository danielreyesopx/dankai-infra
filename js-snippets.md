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

## Snippet 002 — Clean a Phone Number

**Used in:** any workflow that receives phone numbers from Meta or user input
**What it does:** Removes any character that is not a digit — strips +, dashes,
spaces, parentheses. Ensures the phone number is clean for the WhatsApp API.

**Key patterns learned:**
- `.replace(/\D/g, '')` — regex that removes all non-digit characters
- `\D` means "not a digit", `g` means "do it to every match, not just the first"

```javascript
const raw_phone = $input.first().json.phone;
const clean_phone = raw_phone.replace(/\D/g, '');
return [{ json: { phone: clean_phone } }];
```

**The line I'd change:** Line 1 — update `json.phone` to match whatever
field name the incoming data uses.