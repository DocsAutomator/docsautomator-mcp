# Create, send and follow up

## 1. Find the automation

Call `list_automations` with `search` set to the document type, for example "Contracts". If several match, ask which one. Call `get_automation`: it returns the data source, the fields with their stored descriptions, and whether signing or acceptance is on. Follow the stored descriptions.

## 2. Fill the document

**Native route** (see SKILL.md):
- Find the record. DocsAutomator's tools cannot search records. Ask the user, or use your own connector for the tool to find the record id (Airtable `rec...`, Notion page id, ClickUp task id), the Google Sheets row number, or the Google Forms response id.
- Name the record you found in the send question, so the user can see it is the right one.
- Call `create_document` with that id (`recId`, or `rowNumber` for Google Sheets). The record fills every field and its line items, and the signer emails come from its mapped fields. Pass `recipients` only when the user names a different person.
- Never copy the automation and never change its data source to send from the chat.

**Agent route** (the automation's data source is API):
- Read the record and its line items with your own connector (see "Per tool" below).
- Map the values to the fields from `get_automation`. Send line items as `line_items_1` (and `line_items_2` for a second table): an array with one object per row, whose keys are the line-item field names in the template.
  `"line_items_1": [{ "description": "Design", "quantity": 10, "unit_price": 150, "amount": 1500 }]`
- Pass `recipients` with the name and email of every signer. Ask for a missing email address; never guess one.
- Pass `idempotencyKey` built from the tool and the record id, for example `hubspot-deal-1234-contract`. A second call with the same key within 7 days returns the first document and sends nothing.

Ask for every missing value in one question.

## 3. Signing or acceptance

If `get_automation` shows no signing or acceptance step, choose it yourself: signature for contracts and other documents with signature fields, acceptance for quotes and offers. Ask only when neither fits. Then call `configure_esignature`:
- `closeType: "signature"` needs signature fields in the template; `closeType: "acceptance"` needs none.
- On the agent route, signers use `emailSource: "request"`; you name them with each document.
- On the native route, signers come from an email field of the record. Set `signedPdfField` to the record's attachment or file field (a column for Google Sheets), so the signed PDF goes back into the record.

## 4. Test, then live

1. Preview: `create_document` with `mode: "preview"`. Show the PDF. Nothing is sent.
2. Test: the first send of a new setup uses `mode: "test"`. It sends the real email to the real recipient, adds a watermark and does not count toward usage. Ask before you send.
3. Live: ask one question that names the record and both effects, for example "Switch Contracts to live and send the Acme contract to Anna now?". On yes, call `configure_automation` with `status: "live"`, then `create_document` with `mode: "live"`. Going live is a lasting setting: every later document of this automation is real and counts toward the plan.

Every later send still needs the user's yes, unless the user set up a schedule (section 7).

## 5. Follow up

- `list_esign_sessions` with `status: "open"` lists what is still waiting. `waitingOn` names who has not signed or accepted.
- `send_esign_reminder` with the `signerIndex` from `waitingOn` reminds one person. `resend_esign_invite` sends the invitation again. Ask before each one, because both send an email.
- `cancel_esign_session` stops a request; its links stop working. Ask first.

## 6. The signed PDF

`list_esign_sessions` with `sessionId` returns the session; when its status is `completed`, `signedPdfUrl` is the signed PDF.

- **Native route:** with `signedPdfField` set, DocsAutomator has already put the signed PDF into the record. Nothing else to do.
- **Agent route:** write the link and the status back with your connector (see "Per tool"). If the user also wants the file in Google Drive or OneDrive, save it there with their connector.

The signed PDF and its record are always in DocsAutomator too: https://app.docsautomator.co/documents.

## 7. How sends start

- **The user asks** in the chat.
- **Native route, on its own:** DocsAutomator starts when a field gets a value, for example Status becomes "Send contract". Set it with `configure_automation`: `airtableTrigger` or `notionTrigger` (a single select, multiple select or checkbox field and its value), or `sheetsAutoPolling` (new Google Sheets rows or Google Forms responses, about every 5 minutes). `get_automation` with `include: ["generationSetup"]` gives the exact setup for the other sources. Use this, not a schedule, on the native route.
- **Agent route, on a schedule:** a Claude routine or a ChatGPT workspace agent runs at set times with the user's connectors. Write its instructions with the user, for example:

  > Every hour: find HubSpot deals in the stage "Send contract" that have no "Contract session" yet. For each deal: read the deal, its contact and its line items. Create the contract with DocsAutomator automation "Contracts" in live mode, with the contact as signer and idempotencyKey "hubspot-deal-<deal id>-contract". Write the returned session id into "Contract session" and "Sent" into "Contract status". Then find deals with "Contract status" "Sent", check their session with list_esign_sessions, and for each completed one write the signed PDF link into "Signed contract" and "Signed" into "Contract status". If a step fails, write "Failed: <reason>" into "Contract status" and go on with the next deal.

  The user creates the routine in Claude (claude.ai/code/routines) or the agent in ChatGPT. Claude routines need Claude Pro, Max, Team or Enterprise. ChatGPT workspace agents need ChatGPT Business or Enterprise.
- **Agent route, right after signing:** DocsAutomator can start the routine or agent the moment a document is signed or accepted. The user sets this up in the DocsAutomator app, because the token must not pass through the chat: automation editor, Actions, Notify Webhook, Send to "Claude routine" or "ChatGPT agent", paste the trigger URL and the token, Save, then Send test event (expect "Received"). A Claude routine with an API trigger runs as a Claude Code task, so the user sets up Claude Code on the web first (claude.ai/code). The link is https://app.docsautomator.co/automations/<automation id>. The routine receives the session id and the signed PDF link; it finds the record by the stored session id and writes back. The hourly check above still catches anything this misses.

## 8. Reliability

- **No double sends.** On the agent route, every send carries an `idempotencyKey` from the record id, and a scheduled run sends only records without a session id. If a run stops after the send and before it stores the session id, the next run sends with the same key and gets the first document back. On the native route, DocsAutomator sends once each time the trigger field gets its value.
- **A failed write-back.** The session and the signed PDF stay in DocsAutomator. On the agent route, the scheduled check finds completed sessions whose record is not updated yet and writes them. On the native route, if the record has no signed PDF, get `signedPdfUrl` from `list_esign_sessions` and write the link with your connector; without a connector, give the user the link.
- **A failed send.** `get_recent_runs` shows the real error. Fix the cause. Send again only when `list_esign_sessions` shows no session for this record.

## Per tool

**Airtable** (native). Find the record id with your Airtable connector (`search_records` or `list_records_for_table`). DocsAutomator reads the line items from the linked table and puts the signed PDF into the attachment field.

**Google Sheets** (native). Find the row number by reading the sheet. DocsAutomator reads the line items from their tab and writes the signed PDF link into the column you set.

**Notion** (native). Find the page id with your Notion connector (`notion-search` or `notion-fetch`). DocsAutomator reads the related line items and puts the signed PDF into the Files property.

**HubSpot** (agent route; HubSpot's own connector).
- Read: the deal with `get_crm_objects` or `search_crm_objects`, then its associated contact (the signer) and line items. Line item fields: name, quantity, price, amount.
- Write back: `manage_crm_objects` to update deal properties: "Contract session" (the session id), "Contract status" and "Signed contract" (the signed PDF link). Create them if they are missing and tell the user.
- HubSpot's connector can ask to confirm each write. Before the user relies on a schedule, run the routine once by hand and check that the deal was updated.

**Any other tool** (agent route). Read the record and its rows with your connector, send them as in section 2, and write the link and the status back to a link or text field.
