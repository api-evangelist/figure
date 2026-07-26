---
name: Retrieve HELOC application details and documents
description: Fetch details for a Figure HELOC application and download its documents.
api: openapi/figure-heloc-inquiries-openapi-original.yml
operations: [fetchAppDetails, listDocuments, downloadDocument, downloadAllDocuments]
---

# Retrieve HELOC application details and documents

Use the Figure HELOC Inquiries API to inspect an existing application and pull its documents.

## Auth & conventions
- Send the `apikey` and `User-Agent` headers on every request. See `authentication/figure-authentication.yml`.
- Base host: `https://api.figure.com` (or `https://api.test.figure.com` for test).
- You need the application's `appUuid` (returned by `startInquiry`).

## Steps
1. **fetchAppDetails** — GET `/products/heloc/v1/details/{appUuid}` to read current application status and details.
2. **listDocuments** — GET `/products/heloc/v1/document/{appUuid}/list` to enumerate available documents; capture each `documentUuid`.
3. **downloadDocument** — GET `/products/heloc/v1/document/{appUuid}/download/{documentUuid}` to fetch a single document.
4. **downloadAllDocuments** — GET `/products/heloc/v1/document/{appUuid}/download` to fetch all documents at once.

## Errors
Handle 401 (bad/missing apikey) and 400. See `errors/figure-problem-types.yml`.
