# Set up a document

The goal: an automation that makes the user's document from their template and data, with every field stored so that every later document, from any agent, prints the same way.

## 1. Reuse first

Call `list_automations`. If exactly one fits the request, use it and tell the user which one; if several fit, ask. Then go to step 4.

## 2. Template

Use this order:
1. **The user's own template.** A Google Doc link (`create_automation` with `docTemplateLink`, or `set_template` with `googleDocUrl`), or an uploaded .docx or PDF (`set_template` with `file`, `fileUrl` or `fileBase64`).
2. **The gallery.** `search` with `in: "templates"`, then `set_template` with the `galleryTemplateId` of a result that has a Word version.
3. **A new design.** `generate_word_template`, only when nothing fits or the user asks for one. Describe the structure in the prompt: sections, field names in `{{double_braces}}`, line-item tables and tone. Pass `brandWebsite` when the user gave a website. If generation times out, shorten the prompt before you retry.

Build a reusable template: the details of one case become fields. Never fill in a one-off document.

## 3. Data

- **Native route (see SKILL.md):** connect the tool to the automation. Call `get_data_source` to find the base, table, sheet or database and its fields. If the account is not connected, the result gives the link to connect it in the DocsAutomator app; give the user that link. Then call `set_data_source` with the source and the `mappings` (field name to the tool's field). Line items come from a linked table or sheet: the `lineItems` block of `set_data_source`.
- **Agent route, or values typed in the chat:** create the automation with `dataSourceName: "API"`. You supply the values with each document (see `send.md`, agent route).

## 4. Agree the fields

Call `get_automation`. For every field, work out what it means, where its value comes from and how it prints. Propose this yourself from the template, the data and the user's business.

Show the user only these fields, in one table:
- amounts, numbers and percentages,
- dates,
- fields you could not work out.

| Field | What it means | Comes from | Prints as |
|---|---|---|---|
| total | Total including tax, in EUR | Deal amount | €1,234.56 |
| valid_until | Last day the quote is valid | Today plus 30 days | 31 December 2026 |

When the user confirms or corrects it, store every field with `configure_placeholders`: `descriptions` (meaning and print format) and `formats` (one entry per amount, number, percent and date, currency with its ISO code).

Take the locale from the user's language and currency, or from the data, and store it with `configure_automation` (`locale`). Ask only when the language and the currency point to different locales.

Tell the user once: "Every document from now on follows these rules. You can change any field later."

## 5. Optional parts

Set these up only when the user asks or the template needs them:
- Line-item grouping, sorting or numbering: `configure_line_items`.
- Sections that show or hide: `configure_placeholders` with `conditions`.
- Where documents go (email, Google Drive, a record field): `configure_output`.
- Signing or acceptance: see `send.md`.

## 6. Test document

Call `create_document` with `mode: "preview"`. Show the PDF link. Fix what the user wants changed, and preview again.

## Quick start without a template

When the user has no template and wants to see it work:
1. Ask for their email address and, if they like, their website.
2. `create_automation` with `title: "Quotes"` and `dataSourceName: "API"`.
3. A gallery quote (`search` "quote" in templates, then `set_template`). If none fits, `generate_word_template` with `signingMode: "none"`, `brandWebsite`, and a one-page quote: logo, quote number, date, valid-until date, sender and client details, a line-item table (description, quantity, unit price, amount), subtotal, tax, total and terms.
4. Agree the fields (step 4).
5. `configure_esignature` with `closeType: "acceptance"`, `deliveryMethod: "email"` and one signer `{ role: "Client", emailSource: "request" }`.
6. Fill every field with realistic sample data for the user's business. `create_document` with `mode: "test"` and `recipients: [{ signer: 1, email, name }]` set to the user.
7. Tell the user: "Open the email from DocsAutomator on your phone, type your name and tap Accept."

Test mode sends the real email, adds a preview watermark and does not count toward usage. The accepted PDF and its record appear at https://app.docsautomator.co/documents.
