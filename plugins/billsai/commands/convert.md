---
description: Convert a bank statement PDF to CSV (read-only, nothing is saved)
---

Convert a bank statement PDF to CSV with Bills AI. File or request from the user: $ARGUMENTS

1. If the user did not name a PDF, ask which one. Scanned or photographed PDFs are not supported (no OCR).
2. For a small file (up to 2 MB decoded) call `convert_statement_pdf` with `pdf_base64`. For a larger file (up to 10 MB), or if the result was `truncated`, or if you can run shell commands, call `create_statement_upload_url`, then PUT the PDF to the returned one-time URL (valid 10 minutes) with `curl -X PUT --data-binary @file.pdf "<url>"`; the response is the CSV.
3. Show the headers, row count and any warnings exactly as returned (and say if `truncated` is true), and give the CSV.
4. Remind the user to check the output against the original statement. Nothing was saved to Bills AI.
5. If the tools say sign-in is required, tell the user to authenticate `billsai` (open `/mcp`, choose `billsai`, select Authenticate).
