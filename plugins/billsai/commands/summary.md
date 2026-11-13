---
description: Summarize income, expenses and spending by category for a month or year (read-only)
---

Summarize the user's saved Bills AI data. This is read-only. Period from the user: $ARGUMENTS

1. If no year or month was given, call `list_statements` and ask which period to use.
2. Call `get_spending_summary` for that year or month.
3. Report income, expenses, savings rate and the top spending categories exactly as returned.
4. Say which statements the figures cover and that they only include what the user saved in Bills AI. This is not financial advice.
5. If the tools say sign-in is required, tell the user to authenticate `billsai` (open `/mcp`, choose `billsai`, select Authenticate).
