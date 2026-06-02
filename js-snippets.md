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

-------------------------------

---

## Snippet 003 — Format a Date

**Used in:** any workflow that reads timestamps from Supabase and needs
to display them in a human-readable format (reports, WhatsApp messages).
**What it does:** Converts a raw ISO timestamp like `2026-06-02T14:32:00.000Z`
into a clean readable date like `02 de junio de 2026`.

**Key patterns learned:**
- `new Date(raw)` — converts a string into a JavaScript Date object
- `.toLocaleDateString('es-DO', {...})` — formats for Dominican Spanish
- Change `'es-DO'` to `'en-US'` to switch to English output

```javascript
const raw_date = $input.first().json.created_at;
const date = new Date(raw_date);
const formatted = date.toLocaleDateString('es-DO', {
  day: '2-digit',
  month: 'long',
  year: 'numeric'
});
return [{ json: { formatted_date: formatted } }];
```

**The line I'd change:** Line 3 — update `'es-DO'` for a different language,
or `created_at` in Line 1 if the date field has a different name.
