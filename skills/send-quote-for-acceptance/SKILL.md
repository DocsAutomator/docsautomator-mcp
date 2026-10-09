---
name: send-quote-for-acceptance
description: Make a quote in the user's own brand with DocsAutomator and send it to them for acceptance, so they can accept it on their phone and see the accepted PDF in DocsAutomator. Use when the user wants to send a quote, offer or proposal for acceptance, or asks how to start with DocsAutomator from their AI agent.
---

# Send a quote for acceptance

This is the first win with DocsAutomator. You make a quote in the user's brand and send it to the user for acceptance. The user accepts it on their phone and sees the accepted PDF and its record in DocsAutomator under Documents. It takes about two minutes.

## Before you start

- The DocsAutomator MCP server must be connected (`https://mcp.docsautomator.co/mcp`). If its tools are missing, tell the user to add it: in Claude, Settings > Connectors > Add custom connector; in ChatGPT, Settings > Plugins with developer mode on; in Cursor or VS Code, the "Add to" button on https://docsautomator.co/features/ai-and-agents/.
- Ask for three things in one message: the user's email address (the quote goes to them), whether they already have a quote template (a Word file, a PDF or a Google Doc), and, optionally, their website (for the brand).

## Steps

1. Call `create_automation` with `title: "Quotes"` and `dataSourceName: "API"`.
2. Give it a template, in this order:
   - Their own template: call `set_template` with it (`fileBase64` for a Word or PDF file, the link for a Google Doc).
   - Otherwise our gallery: call `search` with `in: "templates"` and the query "quote", show the results that have a Word version, and call `set_template` with the `galleryTemplateId` they pick.
   - Only when none fits, or the user asks for AI: call `generate_word_template` on the new automation with `title: "Quote"`, `signingMode: "none"` (acceptance needs no signature block), `brandWebsite` set to the user's website when they gave one, and a prompt for a one-page quote: logo, quote number, date, valid-until date, sender and client details, a line items table (description, quantity, unit price, amount), subtotal, tax, total, and terms.
3. Never lead with an AI design: ask about their own template first, then offer the gallery.
4. Call `configure_placeholders` with a description for every placeholder and a format for every amount and date. Set the user's `locale` with `configure_automation`.
5. Call `configure_esignature` with `closeType: "acceptance"`, `deliveryMethod: "email"` and one signer `{ role: "Client", emailSource: "request" }`.
6. Call `get_automation` for the placeholders. Fill every placeholder with realistic sample data for the user's business. Send numbers as numbers and dates as ISO dates.
7. Call `create_document` with `mode: "test"` and `recipients: [{ signer: 1, email: <user's email>, name: <user's name> }]`.
8. Tell the user: "Check your inbox on your phone, open the email from DocsAutomator, type your name and tap Accept."

Test mode sends the real email and does not count toward usage. The document carries a preview watermark.

## Done

The first win is done when `list_esign_sessions` with the `sessionId` returns `status: "completed"`. Then give the user two links: the accepted PDF (`signedPdfUrl`) and the record at https://app.docsautomator.co/documents.

## Next steps

Offer one of these:
- "Send it to a real client": call `create_document` again with `mode: "live"`, with the client's email in `recipients`. When the user asks about the price of a real acceptance, call `search` with "signing acceptance price" and quote the result.
- "Now from my Airtable": connect Airtable in the DocsAutomator app under Settings > Integrations, then call `get_data_source` for the table and its fields, and `set_data_source` with the table and the field `mappings`.

## Wording

- Say "accept" and "acceptance". Do not say "sign" for this flow.
- Do not call an acceptance "legally binding".
