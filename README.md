# DocsAutomator MCP server

DocsAutomator makes documents from your data and sends them for acceptance or signature. This repository describes how to connect your AI agent to the hosted DocsAutomator MCP server. MCP (Model Context Protocol) is the open standard that AI apps such as Claude, ChatGPT, Cursor and VS Code use to call outside tools.

**Claude:** DocsAutomator is an official connector in the Claude directory: https://claude.ai/directory/connectors/docsautomator

**Server address for other AI apps:** `https://mcp.docsautomator.co/mcp`

The server is hosted by DocsAutomator. You install nothing. This repository holds no server code, only the connection details, a Claude plugin with a skill, and a Cursor plugin.

## What your agent can do

- Design a document template in your brand, for example from your website.
- Fill the template with your data and make a PDF or Word document.
- Send the document for acceptance (the recipient reads it and clicks Accept) or for signature.
- Follow each document: who opened it, who accepted or signed, and the final PDF.
- Build complete automations that run from Airtable, Notion, Google Sheets, ClickUp, SmartSuite, Google Forms, Zapier, Make or n8n.

## Connect your AI app

You sign in to DocsAutomator the first time your app connects. You need a DocsAutomator account: https://app.docsautomator.co.

**Claude (web, desktop, mobile and Claude Code)**
1. Open DocsAutomator in the Claude directory: https://claude.ai/directory/connectors/docsautomator
2. Click "Connect to Claude".
3. Sign in to DocsAutomator and click Authorize.

In Claude Code, sign in with the same Claude account (`/login`). DocsAutomator then shows in `/mcp` as "claude.ai DocsAutomator", with no extra setup.

Only if you connect Claude Code with an API key instead of a Claude account: `claude mcp add --transport http docsautomator https://mcp.docsautomator.co/mcp --header "Authorization: Bearer YOUR_API_KEY"`.

**ChatGPT**
1. Turn on developer mode in Settings, under Security and login.
2. Add a connector with the address `https://mcp.docsautomator.co/mcp`.
3. Sign in to DocsAutomator and allow access.

**Cursor**

[![Add DocsAutomator MCP server to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](cursor://anysphere.cursor-deeplink/mcp/install?name=DocsAutomator&config=eyJ1cmwiOiJodHRwczovL21jcC5kb2NzYXV0b21hdG9yLmNvL21jcCJ9)

1. Click the button. Cursor opens and shows the DocsAutomator server.
2. Click Install, then Connect.
3. Sign in to DocsAutomator and click Authorize.

Or add this to `.cursor/mcp.json` in your project, or to `~/.cursor/mcp.json` for all projects:

```json
{
  "mcpServers": {
    "docsautomator": { "type": "http", "url": "https://mcp.docsautomator.co/mcp" }
  }
}
```

This repository is also a Cursor plugin: the manifest is `.cursor-plugin/plugin.json` and the server configuration is `mcp.json`.

**VS Code**

[Add to VS Code](vscode:mcp/install?%7B%22name%22%3A%22docsautomator%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.docsautomator.co%2Fmcp%22%7D)

**The DocsAutomator skill (Claude Code)**

The skill teaches your agent how to set up your documents, agree each field with you, send for signature or acceptance, and save the signed PDF back to your records. Install this repository as a plugin. The plugin holds the skill only; the DocsAutomator tools come from the Claude directory connector above.

```
/plugin marketplace add DocsAutomator/docsautomator-mcp
/plugin install docsautomator@docsautomator
```

**Other MCP apps**

Use the address `https://mcp.docsautomator.co/mcp` with streamable HTTP.

## Sign-in

- **OAuth (recommended):** your app opens a DocsAutomator sign-in page. If your account has more than one workspace, you pick the workspace there. To change the workspace, disconnect and connect again.
- **API key:** for apps without OAuth support, send your API key as a Bearer token in the `Authorization` header. Find the key in DocsAutomator under Settings, Workspace, API. An API key belongs to one workspace.

## Your first win: a quote, accepted

Paste this prompt into your AI app:

```
Make a quote in the style of https://your-website.com and send it to me at you@example.com for acceptance. Use DocsAutomator and send it in test mode.
```

No website? Use this one. Your agent then starts from a quote in the DocsAutomator template gallery:

```
Make a quote and send it to me at you@example.com for acceptance. Use DocsAutomator and send it in test mode.
```

Open the email on your phone, check the quote, type your name and tap Accept. The accepted PDF and its record appear in DocsAutomator under Documents. This takes about two minutes.

Next, ask your agent to send the quote to a real client, make it from your Airtable base, or use your own Word template.

## Test mode

Your agent can send any document in test mode. A test document carries a preview watermark and does not count toward your plan. A signing or acceptance request in test mode still sends the real email to the real person, and it is never billed.

## Tools

The server gives your agent 22 tools. Native for Airtable, Google Sheets, Notion and the tools you know: DocsAutomator reads the record, starts on its own and puts the signed PDF back into the record. Your AI agent for everything else, such as HubSpot: your agent reads the record with its own connector, sends the values and writes the result back. In plain words:

- **Automations:** list, read, create, copy and trash automations; set the name, language, status, trigger and output (PDF, Word, Google Doc, Google Drive, email).
- **Templates:** design a Word template with AI in your brand, edit it and undo an edit, or use a gallery template, your own Word or PDF file, or a Google Doc you already have.
- **Placeholders:** what each placeholder means and how it prints, line items, and show and hide rules.
- **Documents:** make a document with the values your agent sends, follow its job, and list recent runs.
- **Record sources (optional):** read the tables and fields of a connected Airtable, Notion, Google Sheets, SmartSuite or ClickUp account, and connect one with its field mappings. You connect the account once in the DocsAutomator app.
- **Acceptance and signature:** set up recipients and settings, list requests with their status, signing links and audit trail, and remind, resend or cancel.
- **Help:** search the DocsAutomator documentation and the template gallery.

Every tool tells your app whether it only reads, changes data, or replaces, deletes or sends something, so your app can ask you before it acts.

## Pricing

Pricing is at https://docsautomator.co/pricing. Signing and acceptance are included in every plan; a signed or accepted document counts as one document.

## Documentation and support

- Documentation: https://docsautomator.co/docs/integrations-api/docsautomator-mcp
- Support: support@docsautomator.co
- Security issues: see [SECURITY.md](SECURITY.md).

## License

The files in this repository are under the MIT License. The DocsAutomator service has its own terms: https://docsautomator.co/terms.
