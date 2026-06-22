WhatsApp AI Chatbot — QA Checklist
DanKai Media | Daniel Reyes
Happy path test

 Send a normal message → AI replies within 5 seconds
 Reply is logged correctly in Supabase (phone, name, message, reply)

Edge case tests

 Send empty message → workflow handles without crashing
 Send message with special characters → no parse errors
 Send from unknown number → new record created correctly

Failure scenarios

 Supabase down → reply still sends, failure is logged
 Claude API timeout → customer receives fallback message
 Malformed Meta payload → JS parse node catches null fields

Delivery check

 All node names are clear and descriptive
 Workflow is documented in plain English
 No hardcoded credentials in exported JSON
 