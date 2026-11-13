# Bills AI for Cursor and Grok Build

> Bills AI turns bank statement PDFs into CSV and analyzes spending without bank linking: an assistant can convert a statement PDF, list saved statements and transactions, summarize spending and export CSV through a hosted, read-only MCP server.

## What it does

Bills AI reads bank statement PDFs into CSV and analyzes spending without bank linking. Uploaded statement files (PDF, images, CSV, XLS/XLSX, up to 10 MB) are read, transactions are extracted and categorized, and recurring charges and spending insights are shown. There is no bank linking and no online-banking credentials are requested. Bills AI is not a bank, adviser or accountant; check extraction against the original statement.

This package connects an AI assistant to the hosted Bills AI MCP server at `https://billsai.cc/api/mcp` (Streamable HTTP, JSON-RPC over POST). Official MCP Registry name: `cc.billsai/billsai`. The assistant can convert a statement PDF to CSV and read what you already saved in Bills AI. It cannot change or delete anything.

## What it contains

| Component | Name | What it does |
|---|---|---|
| MCP server | `billsai` (`https://billsai.cc/api/mcp`) | The six Bills AI tools. |
| Rule | `rules/billsai.mdc` | Text PDFs only, pick the path by size, verify against the original, report as returned. |
| Skill | `convert-a-statement` | Convert a text-layer bank statement PDF to CSV. |
| Skill | `review-spending` | Read saved statements, transactions and the spending summary with honest caveats. |

Example prompts: [examples/prompts.md](examples/prompts.md).

## Install

Add this to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "billsai": {
      "url": "https://billsai.cc/api/mcp",
      "headers": { "Authorization": "Bearer ${env:BILLSAI_API_KEY}" }
    }
  }
}
```

Create a `bai_sk_...` key (paid plans), export it (`export BILLSAI_API_KEY=YOUR_API_KEY`) and restart Cursor. Or use the [install link](https://cursor.com/link/mcp/install?name=billsai&config=eyJ1cmwiOiJodHRwczovL2JpbGxzYWkuY2MvYXBpL21jcCIsImhlYWRlcnMiOnsiQXV0aG9yaXphdGlvbiI6IkJlYXJlciBZT1VSX0FQSV9LRVkifX0%3D), which adds the server with the placeholder `Bearer YOUR_API_KEY`.

Then open Customize, then MCPs, and check that `billsai` shows its tools. Optional: copy `rules/billsai.mdc` to `.cursor/rules/billsai.mdc` in your project, and the folders in `skills/` to `~/.cursor/skills/`.

Grok Build:

```bash
grok mcp add --transport http billsai https://billsai.cc/api/mcp --header "Authorization: Bearer YOUR_API_KEY"
```

## Authentication

Sign-in is OAuth 2.1 with dynamic client registration and PKCE (S256). You sign in to your Bills AI account and approve the connection on a consent screen; there is no key to paste. It works for every Bills AI account, including free ones.

To revoke access, open Bills AI Settings > Connected AI apps and revoke the connection.

Alternative for paid plans (Starter and up): create a `bai_sk_...` API key and send it as `Authorization: Bearer bai_sk_...`.

This kit's `mcp.json` uses the API key alternative (`Authorization: Bearer ${env:BILLSAI_API_KEY}`), so a paid plan is needed for it. Clients that support OAuth for remote MCP servers can use the URL alone: remove the `headers` block and complete the browser sign-in. Never commit a key.

## Tools

| Tool | What it does | Access |
|---|---|---|
| `convert_statement_pdf` | Converts a PDF passed as base64 (small files) to CSV text, with a table preview and warnings. Read in memory, not stored. | Read-only |
| `create_statement_upload_url` | Returns a one-time URL valid for 10 minutes. A shell-capable agent PUTs a PDF up to 10 MB to it with curl; the response is the CSV. Not stored. | Read-only |
| `list_statements` | Your saved statements: bank, date, processing status, transaction count, grade. | Read-only |
| `list_transactions` | Saved transactions, filtered by statement, year, month, category or search, paged. | Read-only |
| `get_spending_summary` | Income, expenses, savings rate and spend by category for a year or month. | Read-only |
| `export_transactions_csv` | Saved transactions as CSV text. | Read-only |

Tool arguments are snake_case. Nothing can be changed or deleted through MCP.

## Data handling

- The convert and upload tools read the PDF in memory and return the CSV only to the connected assistant. Nothing is saved to the Bills AI database or file store, and file names and contents are not logged or sent to error tracking.
- The stored-data tools only read what you already saved in Bills AI. Saved transactions are kept until you delete them; original descriptions and account numbers are encrypted at rest with AES-256-GCM, with keys held by Bills AI. This is not end-to-end encryption.
- Whatever a tool returns is visible to the AI assistant you connected, under that assistant provider's own terms.
- This repository stores nothing and runs no code on your machine. See the [privacy policy](https://billsai.cc/privacy) and [terms](https://billsai.cc/terms).

## Limits

- The converter handles text-layer PDFs only; scanned or photographed PDFs are not supported (no OCR).
- PDFs up to 10 MB. `convert_statement_pdf` takes base64 and suits small files; the upload URL from `create_statement_upload_url` is one-time and valid for 10 minutes.
- Values are copied as printed, with no date or amount normalisation. Statement layouts vary: check output against the original statement.
- Stored-data tools see only statements and transactions you saved in Bills AI.
- The standalone converter at https://billsai.cc/tools/pdf-to-csv has its own per-visitor rate limits.

## Pricing

Plans and what each includes: https://billsai.cc/pricing

## Links

- Product: https://billsai.cc
- MCP page (markdown: https://billsai.cc/mcp.md): https://billsai.cc/mcp
- Free PDF to CSV converter: https://billsai.cc/tools/pdf-to-csv
- Converter accuracy tests: https://billsai.cc/tools/pdf-to-csv/accuracy
- Pricing: https://billsai.cc/pricing
- MCP server card: https://billsai.cc/.well-known/mcp/server-card.json
- Official MCP Registry name: `cc.billsai/billsai`
- Other ways in: [Claude Code plugin](https://github.com/airdropfi/billsai-claude-plugin), [Gemini CLI extension](https://github.com/airdropfi/billsai-gemini-extension)

## Layout

```text
.cursor-plugin/plugin.json   Cursor Plugin manifest (also read by Grok Build)
mcp.json                     MCP server for Cursor (API key from BILLSAI_API_KEY)
.mcp.json                    the same server for Grok Build and other loaders
rules/billsai.mdc            rule
skills/                      two skills (SKILL.md)
assets/logo.png              1024x1024 logo
examples/prompts.md          example prompts
```

## Support

support@billsai.cc

## License

MIT for the files in this repository (see `LICENSE`). The Bills AI service is governed by its terms: https://billsai.cc/terms.
