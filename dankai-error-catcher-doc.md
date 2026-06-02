# DanKai — Error Catcher Workflow

**Last updated:** 2026-06-02
**Status:** Active
**Linked to:** whatsapp_receiver_v1

## What this workflow does
Monitors the WhatsApp Receiver workflow for crashes. When any node fails,
this workflow fires automatically and sends a WhatsApp alert to the admin
with enough information to find and fix the problem without checking n8n manually.

## How data flows (step by step)

1. **Error Trigger** — fires automatically when the linked workflow crashes.
   Receives crash data: which workflow failed, which node was last executed,
   the error message, and the execution ID.

2. **Edit Fields** — extracts and cleans the relevant fields from the crash data:
   - adminPhone: the number that receives the alert (set once here)
   - workflow: name of the workflow that crashed
   - last_execution: name of the node that failed
   - execution_ID: numeric ID of the failed run (use this to find it in n8n)
   - error_Message: the human-readable error text

3. **Send WhatsApp Error Alert** — HTTP Request node calling the Meta Cloud API.
   Sends a formatted WhatsApp message to the admin number with all five fields.
   Uses JSON.stringify() in the message body to safely handle quotes inside
   error messages (which would otherwise break the JSON structure).

## Where it could fail
- Error Catcher is not activated → crashes happen silently, no alert sent
- Error Workflow not linked in main workflow Settings → catcher never fires
- adminPhone number is wrong or not on WhatsApp → message sends but never arrives
- Meta API token expires → HTTP node gets a 401 and the alert fails to send
