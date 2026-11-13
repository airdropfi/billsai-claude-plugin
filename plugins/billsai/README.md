# Bills AI for Claude Code

> Bills AI turns bank statement PDFs into CSV and analyzes spending without bank linking: an assistant can convert a statement PDF, list saved statements and transactions, summarize spending and export CSV through a hosted, read-only MCP server.

## What it does

Bills AI reads bank statement PDFs into CSV and analyzes spending without bank linking. Uploaded statement files (PDF, images, CSV, XLS/XLSX, up to 10 MB) are read, transactions are extracted and categorized, and recurring charges and spending insights are shown. There is no bank linking and no online-banking credentials are requested. Bills AI is not a bank, adviser or accountant; check extraction against the original statement.

This package connects an AI assistant to the hosted Bills AI MCP server at `https://billsai.cc/api/mcp` (Streamable HTTP, JSON-RPC over POST). Official MCP Registry name: `cc.billsai/billsai`. The assistant can convert a statement PDF to CSV and read what you already saved in Bills AI. It cannot change or delete anything.

## What it contains

| Component | Name | What it does |
|---|---|---|
| MCP server | `billsai` (`https://billsai.cc/api/mcp`) | The six Bills AI tools. Sign-in is OAuth; there is no key to paste. |
| Skill | `convert-a-statement` | Convert a text-layer bank statement PDF to CSV via base64 or the one-time upload URL, and verify against the original. |
| Skill | `review-spending` | Read saved statements, transactions and the spending summary with honest caveats. |
| Command | `/billsai:convert` | Convert a statement PDF to CSV. |
| Command | `/billsai:transactions` | List saved transactions with optional filters. |
| Command | `/billsai:summary` | Income, expenses and spending by category for a month or year. |

## Install

```text
/plugin marketplace add airdropfi/billsai-claude-plugin
/plugin install billsai@billsai-plugins
/reload-plugins
```

From a shell: `claude plugin marketplace add airdropfi/billsai-claude-plugin` then `claude plugin install billsai@billsai-plugins`.

Then open `/mcp`, choose `billsai` and select Authenticate. To try the plugin from a local checkout without a marketplace: `claude --plugin-dir ./plugins/billsai`.

## Authentication

Sign-in is OAuth 2.1 with dynamic client registration and PKCE (S256). You sign in to your Bills AI account and approve the connection on a consent screen; there is no key to paste. It works for every Bills AI account, including free ones.

To revoke access, open Bills AI Settings > Connected AI apps and revoke the connection.

Alternative for paid plans (Starter and up): create a `bai_sk_...` API key and send it as `Authorization: Bearer bai_sk_...`.

To use an API key instead, add the server yourself: `claude mcp add --transport http billsai https://billsai.cc/api/mcp --header "Authorization: Bearer bai_sk_YOUR_KEY"`.

## Example prompts

- `Convert this bank statement PDF to CSV and show me the warnings. Tell me what to check against the original.`
- `Where did my money go in March? Use my saved Bills AI statements.`
- `List my saved transactions in the Dining category for this year.`
- `Search my saved transactions for "subscription" and show the amounts.`

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
- Other ways in: [Cursor kit](https://github.com/airdropfi/billsai-cursor-kit), [Gemini CLI extension](https://github.com/airdropfi/billsai-gemini-extension)

## Layout

```text
.claude-plugin/plugin.json   plugin manifest
.mcp.json                   hosted MCP server billsai
commands/                   /billsai:convert, /billsai:transactions, /billsai:summary
skills/                     convert-a-statement, review-spending
assets/icon.png             512x512 icon
```

## Support

support@billsai.cc

## License

MIT for the files in this repository (see `LICENSE`). The Bills AI service is governed by its terms: https://billsai.cc/terms.
