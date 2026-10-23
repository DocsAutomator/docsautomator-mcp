---
name: docsautomator
description: Create quotes, contracts, invoices and other documents from your own template and data with DocsAutomator. Send them for signature or acceptance, follow up, and save the signed PDF and status back to Airtable, Google Sheets, Notion, HubSpot or another connected tool. Use when the user wants to set up, create, send or check a document, run it on a schedule, or act after it is signed.
license: MIT
---

# DocsAutomator

Native for Airtable, Google Sheets, Notion and the tools you know. Your AI agent for everything else.

DocsAutomator fills the user's own template with their data, makes a PDF or Word document, sends it for signature or acceptance, and files the result back where the record lives.

## Before you start

If no DocsAutomator tools are available, tell the user to connect DocsAutomator, and stop:
- Claude: the DocsAutomator connector in the Claude directory, https://claude.ai/directory/connectors/docsautomator
- ChatGPT and Codex: DocsAutomator in the plugin directory.
- Other apps: the server address `https://mcp.docsautomator.co/mcp`.

The user needs a DocsAutomator account: https://app.docsautomator.co.

## Rules that always apply

- Ask in plain text when you need a choice. Ask before every call that sends an email or makes a live document.
- Send raw values: numbers as numbers, dates as ISO dates (2026-10-01). DocsAutomator formats them.
- Never invent amounts, names, dates or email addresses. Ask for them.
- Address fields by their name. Ids of fields, records and tables are case-sensitive: use an id exactly as a tool returned it in this run, or read it again from the tool's schema. Never copy or retype an id from a card, a message or an earlier chat, and never write ids into routine instructions.
- Say "field" to the user, not "placeholder".
- Say "accept" and "acceptance" for the acceptance flow. Never call an acceptance "legally binding".
- Quote prices only from `get_profile`. Signing and acceptance are included in every paid plan.
- Templates come in this order: the user's own, then the gallery, then a new AI design. Never create or edit a Google Doc.

## Two routes for the user's data

Pick the route from the tool that holds the record. Do not ask the user which route.

- **Native route:** Airtable, Google Sheets, Notion, SmartSuite, ClickUp, Google Forms. DocsAutomator connects to the tool itself. It reads the record with its line items, starts on its own when a field gets a value, and puts the real signed PDF file into the record. Use this route whenever the tool is one of these. Your own connector for the tool only helps to find a record id.
- **Agent route:** HubSpot and every other tool. You read the record and its line items with your own connector for that tool, send the values, and write the PDF link and the status back with the same connector.

## What to read next

| The user wants to | Read |
|---|---|
| Set up a new document, use their template, connect their data, or fix how a field prints | `references/set-up.md` |
| Create or send a document, check who has signed, send a reminder, get the signed PDF, run sends on a schedule, or have something happen after signing | `references/send.md` |

If the user only asks a question about DocsAutomator, call `search` and answer from the result.
