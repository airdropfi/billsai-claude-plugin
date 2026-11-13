---
description: Show saved Bills AI transactions with optional filters (read-only)
---

Show the user's saved Bills AI transactions. This is read-only. Filter or topic from the user: $ARGUMENTS

1. Call `list_transactions` using `year`, `month`, `category`, `statement` or `search` if the user gave a filter. Page only if asked for more.
2. Show date, description, amount and category as a short table, exactly as returned.
3. If the user wants a file, call `export_transactions_csv` with the same filters.
4. Note that transactions were extracted automatically and should be checked against the original statement.
5. If the tools say sign-in is required, tell the user to authenticate `billsai` (open `/mcp`, choose `billsai`, select Authenticate).
