---
name: send-quote-for-acceptance
description: Make a quote in the user's own brand with DocsAutomator and send it to them for acceptance, so they can accept it on their phone and see the accepted PDF in DocsAutomator. Use when the user wants to send a quote, offer or proposal for acceptance, or asks how to start with DocsAutomator from their AI agent.
---

# Send a quote for acceptance

This is the first win with DocsAutomator. You make a quote in the user's brand and send it to the user for acceptance. The user accepts it on their phone and sees the accepted PDF and its record in DocsAutomator under Documents. It takes about two minutes.

## Before you start

- The DocsAutomator MCP server must be connected (`https://mcp.docsautomator.co/mcp`). If its tools are missing, tell the user to add it: in Claude, Settings > Connectors > Add custom connector; in ChatGPT, Settings > Plugins with developer mode on; in Cursor or VS Code, the "Add to" button on https://docsautomator.co/features/ai-and-agents/.
- Ask for two things in one message: the user's email address (the quote goes to them) and, optionally, their website (for the brand).

## Steps

1. Call `create_automation` with `title: "Quotes"` and `dataSourceName: "API"`.
2. If the user gave a website, call `extract_brand_from_url` with it.
3. Call `generate_word_template` on the new automation:
   - `title: "Quote"`, `signingMode: "none"` (acceptance needs no signature block).
   - `customColors` from the brand colors. Pass `logoUrl` only when `logoIsEmbeddable` is true.
   - A prompt for a one-page quote: logo, quote number, date, valid-until date, sender and client details, a line items table (description, quantity, unit price, amount), subtotal, tax, total, and terms.
   - `placeholderDescriptions` for every placeholder.
4. If there is no website, or step 3 fails: call `search_template_gallery` with "quote", then `use_gallery_template` with a result that has a Word version.
5. Call `configure_esignature` with `closeType: "acceptance"`, `deliveryMethod: "email"` and one signer `{ role: "Client", emailSource: "request" }`.
6. Call `list_placeholders`. Fill every placeholder with realistic sample data for the user's business.
7. Call `create_document` with `mode: "test"` and `recipients: [{ signer: 1, email: <user's email>, name: <user's name> }]`.
8. Tell the user: "Check your inbox on your phone, open the email from DocsAutomator, type your name and tap Accept."

Test mode is free. It sends the real email and is never billed. The document carries a preview watermark.

## Done

The first win is done when `get_esign_session` returns `status: "completed"`. Then give the user two links: the accepted PDF (`signedPdfUrl`) and the record at https://app.docsautomator.co/documents.

## Next steps

Offer one of these:
- "Send it to a real client": call `create_document` again with `mode: "live"`, with the client's email in `recipients`. A real acceptance costs $0.50 per document, charged when the recipient first opens it.
- "Now from my Airtable": connect Airtable in the DocsAutomator app under Settings > Integrations, then call `set_data_source` and `set_field_mappings`.
- "Use my own Word template": call `upload_word_template` with the user's .docx file.

## Wording

- Say "accept" and "acceptance". Do not say "sign" for this flow.
- Do not call an acceptance "legally binding".
