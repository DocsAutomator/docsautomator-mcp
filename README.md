# DocsAutomator MCP server

DocsAutomator MCP is a hosted MCP (Model Context Protocol) server for document generation. It sets up document automations from your own templates and data, and works with Claude, ChatGPT, Cursor, VS Code and other MCP clients.

You ask Claude or ChatGPT once to set up a document automation: your template, your data source, the field mappings and where the finished document goes. After that, the native integration makes the documents on autopilot from your own data, for example from Airtable, Google Sheets or Notion. Each document comes out the same way every time, because it uses your template and your mappings, not a new AI answer. Signing or acceptance is optional.

- **Claude:** DocsAutomator is an official connector in the Claude directory: https://claude.ai/directory/connectors/docsautomator
- **ChatGPT:** DocsAutomator is an official plugin in ChatGPT Plugins: https://chatgpt.com/plugins/plugin_asdk_app_6abc8c22ec94819186aac8114b0e24b3
- **Other MCP clients:** `https://mcp.docsautomator.co/mcp` (streamable HTTP)

DocsAutomator hosts the server, so you install nothing. This repository holds no server code. It holds the connection details, a Claude plugin with a skill, and a Cursor plugin.

## How it works

1. **Set up once in a chat.** Claude or ChatGPT creates the automation, adds your template (your own Google Doc, Word or PDF file, a gallery template, or a new Word template in your brand), connects your data source and maps each placeholder to a field.
2. **Check it.** The first document is a test document from a real record (see Test mode).
3. **Run on autopilot.** The integration makes each document from your record, with no chat and no AI. The PDF and its link can go back to the record, to Google Drive or OneDrive, or out by email.
4. **Optional: signing or acceptance.** The recipient signs the document, or reads it and clicks Accept. The signed or accepted PDF and its status go back to the record.

DocsAutomator has native integrations for Airtable, Google Sheets, Google Forms, Notion, SmartSuite and ClickUp, and works with Zapier, Make, n8n, Glide, Softr and Noloco.

When your data lives in a tool without an integration, the AI client can send the values for each document itself.

## Connect your AI client

You need a DocsAutomator account: https://app.docsautomator.co. You sign in to DocsAutomator the first time your client connects.

**Claude (web, desktop, mobile and Claude Code)**
1. Open DocsAutomator in the Claude directory: https://claude.ai/directory/connectors/docsautomator
2. Click "Connect to Claude".
3. Sign in to DocsAutomator and click Authorize.

In Claude Code, sign in with the same Claude account (`/login`). DocsAutomator then shows in `/mcp` as "claude.ai DocsAutomator", with no extra setup.

**ChatGPT (web, desktop and mobile)**
1. Open DocsAutomator in ChatGPT Plugins: https://chatgpt.com/plugins/plugin_asdk_app_6abc8c22ec94819186aac8114b0e24b3.
2. Click "Install plugin", then "Connect".
3. Sign in to DocsAutomator and allow access.

You need no server address, no API key and no developer mode. On an Enterprise or Edu plan, an admin may have to enable DocsAutomator first.

**Cursor**

[![Add DocsAutomator MCP server to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](cursor://anysphere.cursor-deeplink/mcp/install?name=DocsAutomator&config=eyJ1cmwiOiJodHRwczovL21jcC5kb2NzYXV0b21hdG9yLmNvL21jcCJ9)

Click the button, click Install, then Connect, and sign in to DocsAutomator. Or add this to `.cursor/mcp.json` in your project, or to `~/.cursor/mcp.json` for all projects:

```json
{
  "mcpServers": {
    "docsautomator": { "type": "http", "url": "https://mcp.docsautomator.co/mcp" }
  }
}
```

**VS Code**

[Add to VS Code](vscode:mcp/install?%7B%22name%22%3A%22docsautomator%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.docsautomator.co%2Fmcp%22%7D)

Click the link, click Install, then sign in to DocsAutomator.

**Other MCP clients**

Use the address `https://mcp.docsautomator.co/mcp` with streamable HTTP. Sign in with OAuth, or send an API key (see below).

**Claude Code skill**

To add the "Send a quote for acceptance" skill, install this repository as a plugin. The plugin holds the skill only; the tools come from the Claude directory connector above.

```
/plugin marketplace add DocsAutomator/docsautomator-mcp
/plugin install docsautomator@docsautomator
```

## Sign-in

- **OAuth (recommended):** your client opens a DocsAutomator sign-in page. If your account has more than one workspace, you pick the workspace there. To change the workspace, disconnect and connect again.
- **API key:** for clients without OAuth, send your API key as a Bearer token in the `Authorization` header. Find the key in DocsAutomator under Settings, Workspace, API. An API key belongs to one workspace.

## First prompt to try

```
Set up a DocsAutomator automation that makes a quote from my Airtable base, and make a test document from one record.
```

Use Google Sheets, Notion or another source in place of Airtable. Your AI client asks for your template first. When you have none, it uses a gallery template or designs one in your brand.

## Tools

The server has 22 tools. Each tool says whether it reads, changes, deletes or sends, so your client can ask you first.

| Area | Tools |
|---|---|
| Automations | `get_profile`, `list_automations`, `get_automation`, `create_automation`, `configure_automation` |
| Templates | `set_template`, `generate_word_template`, `edit_word_template`, `undo_template_edit`, `search` |
| Placeholders | `configure_placeholders`, `configure_line_items` |
| Data sources | `get_data_source`, `set_data_source` |
| Output and documents | `configure_output`, `create_document`, `get_recent_runs` |
| Signing and acceptance | `configure_esignature`, `list_esign_sessions`, `send_esign_reminder`, `resend_esign_invite`, `cancel_esign_session` |

- **Automations:** read and create automations, copy one, and set the name, status, language, date format and automatic triggers.
- **Templates:** use your own Google Doc, Word or PDF file or a gallery template, or design and edit a Word template with AI. `search` also searches the DocsAutomator documentation.
- **Placeholders:** what each placeholder means and how it prints (currency, decimals, dates), line items, and show and hide rules.
- **Data sources:** read the tables and fields of a connected Airtable, Google Sheets, Google Forms, Notion, SmartSuite or ClickUp account, and map each placeholder to a field. You connect the account once in the DocsAutomator app.
- **Output and documents:** choose PDF, Word or Google Doc, and send it by email, to Google Drive or OneDrive, or back to the record. Make a document and see recent runs with their real error messages.
- **Signing and acceptance:** signers, order and reminders; the status of each request; remind, resend or cancel.

## Test mode

A test document carries a preview watermark and does not count toward your plan. A signing or acceptance request in test mode still sends the real email to the real person, and it is never billed.

## Pricing

Pricing is at https://docsautomator.co/pricing. Signing and acceptance are included in every paid plan; a signed or accepted document counts as one document.

## Documentation and support

- Documentation: https://docsautomator.co/docs/integrations-api/docsautomator-mcp
- Support: support@docsautomator.co
- Security issues: see [SECURITY.md](SECURITY.md).

## License

The files in this repository are under the MIT License. The DocsAutomator service has its own terms: https://docsautomator.co/terms.
