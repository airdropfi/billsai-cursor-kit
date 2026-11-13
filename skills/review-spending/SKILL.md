---
name: review-spending
description: Review a user's saved Bills AI statements and transactions with read-only tools and report spending by category, income and recurring patterns with honest caveats. Use when the user asks where their money went, wants a spending summary for a month or year, or asks to find or export saved transactions.
license: MIT
compatibility: Needs the Bills AI MCP server connected and signed in. Read-only; only transactions the user already saved in Bills AI are visible.
---

# Review spending from saved Bills AI data

All tools used here are read-only. Nothing can be changed or deleted through MCP.

## Tools

- `list_statements`: the user's saved statements (bank, date, processing status, transaction count, grade).
- `list_transactions`: saved transactions, filtered by `statement`, `year`, `month`, `category` or `search`, paged.
- `get_spending_summary`: income, expenses, savings rate and spend by category for a `year` or a `month`.
- `export_transactions_csv`: saved transactions as CSV text.

Tool arguments are snake_case.

## Steps

1. Call `list_statements` to see what is saved and whether processing finished. Say which periods are covered and which are missing.
2. For an overview call `get_spending_summary` for the year or month the user names.
3. To explain a number, call `list_transactions` with the category, month or a search term, and page only as far as needed.
4. For a spreadsheet, call `export_transactions_csv` and give the user the CSV text.
5. Report figures exactly as returned. Do not estimate months that are not saved.

## Caveats to state

- The summary covers only statements the user saved in Bills AI; it is not a complete picture of their finances.
- Transactions were extracted and categorized automatically, so categories and amounts can be wrong. The user should check against the original statement.
- This is not financial, tax or accounting advice. Bills AI is not a bank, adviser or accountant.
- What a tool returns is visible to the AI assistant the user connected, under that assistant provider's own terms.
