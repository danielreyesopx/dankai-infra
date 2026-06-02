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

---

## Snippet 004 — Build a WhatsApp Message String

**Used in:** any workflow that needs to send a dynamic, personalized message
**What it does:** Takes a lead's name and message from incoming data and
builds a formatted WhatsApp reply string using a template.

**Key patterns learned:**
- `.map()` — loops over all incoming items and transforms each one
- Backtick strings with `${}` — template literals, inject variables into text
- `|| 'default'` — fallback if field is missing

```javascript
const items = $input.all();
const results = items.map(item => {
  const name = item.json.name || 'Cliente';
  const message = item.json.message || '';
  const reply = `Hola ${name}, recibimos tu mensaje: "${message}". En breve te respondemos.`;
  return { json: { reply } };
});
return results;
```

**The line I'd change:** Line 5 — rewrite the template text to match
whatever message DanKai needs to send.

---

## Snippet 005 — Filter an Array

**Used in:** any workflow that gets multiple rows from Supabase and needs
only the ones matching a condition (e.g. only hot leads, only active clients).
**What it does:** Filters incoming items and returns only those where
a field matches a specific value.

**Key patterns learned:**
- `.filter()` — keeps only items where the condition is true
- `===` — strict equality ("exactly equals", not just similar)

```javascript
const items = $input.all();
const hot_leads = items.filter(item => item.json.status === 'hot');
return hot_leads.map(item => ({ json: item.json }));
```

**The line I'd change:** Line 2 — update `status` to the field you're
filtering on, and `'hot'` to the value you're looking for.

---

## Snippet 006 — Parse a Webhook Payload Safely

**Used in:** any workflow where the webhook body might arrive as a string
instead of a parsed object (common with some third-party integrations).
**What it does:** Checks if the body is a string and parses it if needed,
then extracts the phone field safely.

**Key patterns learned:**
- `typeof x === 'string'` — checks the data type of a value
- `JSON.parse()` — converts a JSON string into a JavaScript object
- Ternary `condition ? x : y` — one-line if/else

```javascript
const raw = $input.first().json;
const body = typeof raw.body === 'string' ? JSON.parse(raw.body) : raw.body ?? raw;
const phone = body.phone || body.from || '';
return [{ json: { phone } }];
```

**The line I'd change:** Line 3 — update field names to match whatever
the incoming webhook actually sends.


---

## Snippet 007 — Reshape JSON for an API

**Used in:** any workflow that needs to repackage internal data into the
specific shape an external API or tool expects.
**What it does:** Picks specific fields from incoming data, renames them,
and adds hardcoded values where needed.

**Key patterns learned:**
- Field renaming: `contact_name: item.name` — new name on left, source on right
- Hardcoded values: `source: 'whatsapp'` — fixed value, not from data
- `??` fallback: `item.lead_status ?? 'new'` — default if field is missing

```javascript
const item = $input.first().json;
return [{
  json: {
    contact_name: item.name,
    contact_phone: item.phone,
    source: 'whatsapp',
    created: item.created_at,
    status: item.lead_status ?? 'new'
  }
}];
```

**The line I'd change:** Add or remove fields to match whatever shape
the target API expects.

---

## Snippet 008 — Build a Summary String

**Used in:** workflows that generate reports, alerts, or multi-field
WhatsApp messages from lead or conversation data.
**What it does:** Combines multiple fields into one clean, readable
multi-line string.

**Key patterns learned:**
- Multi-line template literals — backticks let you span multiple lines
- `.trim()` — removes blank lines/spaces from start and end of string

```javascript
const item = $input.first().json;
const summary = `
Lead: ${item.name} (${item.phone})
Message: ${item.message}
Status: ${item.lead_status ?? 'new'}
Received: ${item.created_at}
`.trim();
return [{ json: { summary } }];
```

**The line I'd change:** Add or remove fields inside the template to
match whatever summary format you need.

---

## Snippet 009 — Check if a Lead Already Exists

**Used in:** whatsapp_receiver_v1 — before creating a new lead, check
if this phone number already has a Supabase record.
**What it does:** Takes the Supabase query result and returns a boolean
flag (true/false) that the If node can route on.

**Key patterns learned:**
- `items.length > 0` — checks if any rows were returned
- `!= null` — checks that a field has a real value
- `&&` — both conditions must be true for the result to be true
- Output is `true` or `false` — clean for If node routing

```javascript
const items = $input.all();
const exists = items.length > 0 && items[0].json.id != null;
return [{ json: { lead_exists: exists } }];
```

**The line I'd change:** `items[0].json.id` — update `id` to whichever
field confirms a real record exists in your table.


---

## Snippet 010 — Idempotency Check

**Used in:** whatsapp_receiver_v1 — prevents duplicate replies when
Meta sends the same webhook more than once.
**What it does:** Checks if a message ID has already been processed.
If yes, returns skip:true so the If node exits early.

**Key patterns learned:**
- Idempotency: process each unique event exactly once
- message_id as the unique key — store it after processing
- Multiple early exit points — return skip:true to stop the workflow
- `??` fallback for boolean fields

```javascript
const item = $input.first().json;
const message_id = item.message_id || item.id || '';
if (!message_id) {
  return [{ json: { skip: true, reason: 'no message_id' } }];
}
const already_processed = item.processed ?? false;
if (already_processed) {
  return [{ json: { skip: true, reason: 'duplicate message' } }];
}
return [{ json: { message_id, skip: false } }];
```

**The line I'd change:** `item.processed` — update to match whatever
field name you use in Supabase to track processed messages.

**Why it matters for DanKai:** Meta sends duplicate webhooks when your
server is slow. Without this check, the customer receives two identical
WhatsApp replies — looks broken and unprofessional.