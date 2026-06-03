# QA Checklist Template — n8n Workflows

Use this before marking any workflow as production-ready.
Copy this file and fill it in for each workflow you ship.

---

## Workflow: [NAME HERE]
**Date tested:** 
**Tested by:** Daniel

---

## 1. Happy Path
- [ ] Send a normal, complete input
- [ ] Verify the full workflow runs without errors
- [ ] Verify the output is correct (right message, right data saved)
- [ ] Check Supabase — did the record save correctly?

**Test input used:**
**Result:**

---

## 2. Empty Input
- [ ] Send a message with no text body (voice note, image, sticker)
- [ ] Verify the If node catches it and skips gracefully
- [ ] Verify no error is thrown — silent skip, not a crash
- [ ] Verify the customer does NOT receive a broken reply

**Test input used:**
**Result:**

---

## 3. Bad Input
- [ ] Send a malformed phone number (missing digits, dashes, wrong format)
- [ ] Send a message in an unexpected language or format
- [ ] Verify the workflow handles it without crashing
- [ ] Verify the Error Catcher fires if it does crash

**Test input used:**
**Result:**

---

## 4. Dependency Down
- [ ] Simulate Supabase failure (change table name temporarily)
- [ ] Simulate AI failure (use wrong API key temporarily)
- [ ] Verify the Error Catcher fires and sends WhatsApp alert
- [ ] Verify the customer does NOT receive a broken or duplicate reply
- [ ] Restore everything immediately after testing

**Test input used:**
**Result:**

---

## Sign-off
- [ ] All 4 scenarios tested
- [ ] Error Catcher confirmed working
- [ ] Workflow saved and committed to GitHub
- [ ] Documentation updated

**Ready for production:** YES / NO