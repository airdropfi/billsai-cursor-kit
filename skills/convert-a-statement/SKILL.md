---
name: convert-a-statement
description: Convert a bank statement PDF into CSV with the Bills AI MCP tools, using base64 for small files or a one-time upload URL for larger files. Use when the user asks to turn a statement PDF into CSV or a spreadsheet, or to extract transactions from a bank statement PDF.
license: MIT
compatibility: Needs the Bills AI MCP server connected and signed in. The converter reads text-layer PDFs only (no OCR). Read-only; nothing is saved.
---

# Convert a bank statement PDF to CSV

This skill changes nothing in the user's Bills AI account. The conversion reads the PDF in memory and returns the CSV only to the connected assistant; nothing is saved to the Bills AI database or file store.

## Choose the path

1. **Small file, no shell:** read the PDF, base64-encode it and call `convert_statement_pdf`. It returns CSV text, a table preview and warnings.
2. **Larger file, or an agent that can run shell commands:** call `create_statement_upload_url`. It returns a one-time URL valid for 10 minutes. Send the PDF to it with `curl` as an HTTP PUT (for example `curl -X PUT --data-binary @statement.pdf "<url>"`). The response of the PUT is the CSV. PDFs up to 10 MB are accepted; the upload is not stored.

If the file is too large to pass as base64, use the upload URL path. If the URL expires, request a new one.

## Limits and honesty

- Text-layer PDFs only. Scanned or photographed PDFs are not supported: there is no OCR. Say so and suggest uploading the file to Bills AI itself, which also accepts images, CSV and XLS/XLSX up to 10 MB, if the user wants that.
- Values are copied as printed; dates and amounts are not normalised.
- Statement layouts vary. Always tell the user to check the CSV against the original statement, and report any warnings the tool returned.
- Do not invent rows or fix numbers the tool did not return.

## Steps

1. Confirm which PDF the user means and whether it is a text PDF.
2. Pick the path above and call the tool.
3. Show the table preview and any warnings. Offer the CSV text or save it to a file the user names.
4. Remind the user to compare totals and a few rows with the original statement.
5. If the tools say sign-in is required, tell the user to authenticate the `billsai` server in their client.
