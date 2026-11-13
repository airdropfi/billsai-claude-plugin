# Bills AI plugins for Claude Code

> Bills AI turns bank statement PDFs into CSV and analyzes spending without bank linking: an assistant can convert a statement PDF, list saved statements and transactions, summarize spending and export CSV through a hosted, read-only MCP server.

## What it does

Bills AI reads bank statement PDFs into CSV and analyzes spending without bank linking. Uploaded statement files (PDF, images, CSV, XLS/XLSX, up to 10 MB) are read, transactions are extracted and categorized, and recurring charges and spending insights are shown. There is no bank linking and no online-banking credentials are requested. Bills AI is not a bank, adviser or accountant; check extraction against the original statement.

This package connects an AI assistant to the hosted Bills AI MCP server at `https://billsai.cc/api/mcp` (Streamable HTTP, JSON-RPC over POST). Official MCP Registry name: `cc.billsai/billsai`. The assistant can convert a statement PDF to CSV and read what you already saved in Bills AI. Nothing can be added, changed or deleted through the server.

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

Optional alternative for paid plans (Starter and up): a `bai_sk_...` API key sent as `Authorization: Bearer bai_sk_...`. Paid-plan API keys are created with the Bills AI account API; there is no settings page for them. The connection token from OAuth expires after 90 days.

With a paid-plan key, optionally add the server yourself instead: `claude mcp add --transport http billsai https://billsai.cc/api/mcp --header "Authorization: Bearer bai_sk_YOUR_KEY"`.

## Example prompts

- `Convert this bank statement PDF to CSV and show me the warnings. Tell me what to check against the original.`
- `Where did my money go in March? Use my saved Bills AI statements.`
- `List my saved transactions in the Dining category for this year.`
- `Search my saved transactions for "subscription" and show the amounts.`

## Tools

| Tool | What it does | Access |
|---|---|---|
| `convert_statement_pdf` | Converts a PDF to CSV. Takes `pdf_base64` (base64 of the PDF, up to 2 MB decoded) or, in ChatGPT only, a `file` attachment; optional `delimiter` (`,` `;` tab `|`) and `include_headers`. Returns `csv`, `headers`, `row_count`, `column_count`, `extraction_method`, `warnings`, `truncated`, `returned_rows` and `note`. A result holds at most about 100,000 characters of CSV. Read in memory, not stored. | Read-only |
| `create_statement_upload_url` | Returns a one-time URL valid for 10 minutes. A shell-capable agent PUTs a PDF up to 10 MB to it with curl; the response is the CSV with every row. Not stored. | Read-only |
| `list_statements` | Your saved statements: bank, date, processing status, transaction count, grade. | Read-only |
| `list_transactions` | Saved transactions, filtered by statement, year, month, category or search, paged. | Read-only |
| `get_spending_summary` | Income, expenses, savings rate and spend by category for a year or month. | Read-only |
| `export_transactions_csv` | Saved transactions as CSV text. | Read-only |

Tool arguments are snake_case. Nothing can be added, changed or deleted through the server.

## Data handling

- The convert and upload tools read the PDF in memory and return the CSV only to the connected assistant. Nothing is saved to the Bills AI database or file store, and file names and contents are not logged by our code.
- The stored-data tools only read what you already saved in Bills AI. Saved transactions are kept until you delete them; original descriptions and account numbers are encrypted at rest with AES-256-GCM, with keys held by Bills AI. This is not end-to-end encryption.
- Whatever a tool returns is visible to the AI assistant you connected, under that assistant provider's own terms.
- This repository stores nothing and runs no code on your machine. See the [privacy policy](https://billsai.cc/privacy) and [terms](https://billsai.cc/terms).

## Limits

- The converter handles text-layer PDFs only; scanned or photographed PDFs are not supported (no OCR).
- `convert_statement_pdf` takes up to 2 MB decoded and a result holds at most about 100,000 characters of CSV; PDFs up to 10 MB go through the upload URL, which returns every row. The upload URL from `create_statement_upload_url` is one-time and valid for 10 minutes.
- Values are copied as printed, with no date or amount normalisation. Statement layouts vary: check output against the original statement.
- Stored-data tools see only statements and transactions you saved in Bills AI.
- Conversions: 10 a minute and 40 a day per account. Other calls: 120 a minute. The connection token expires after 90 days; reconnect to continue.
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
.claude-plugin/marketplace.json     marketplace billsai-plugins
plugins/billsai/                    the plugin: manifest, .mcp.json, commands, skills
scripts/validate.mjs                offline validator (node scripts/validate.mjs)
```

## Support

support@billsai.cc

## License

MIT for the files in this repository (see `LICENSE`). The Bills AI service is governed by its terms: https://billsai.cc/terms.
