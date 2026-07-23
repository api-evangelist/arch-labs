---
name: Collect tax documents from Arch
description: List an investor's tax documents, inspect a specific one, and download its files for tax preparation.
api: openapi/arch-labs-client-api-openapi.json
operations:
- get-list-tax-documents
- get-tax-document-by-id
- get-tax-document-list-files
- get-tax-document-download-file
---

# Collect tax documents from Arch

Use the Arch Client API to retrieve tax documents (e.g. K-1s) Arch has collected for an investor and download the underlying files.

## Prerequisites
- Authenticate: `POST /client-api/v0/auth/token` → `authorization: Bearer <token>`.
- Base URL: `https://arch.co/client-api/v0`. The user role must include tax access.

## Steps
1. **List tax documents** — call `get-list-tax-documents` (`GET /client-api/v0/tax-documents`). Results paginate as Pages (default 25, up to 1000).
2. **Fetch a specific tax document** — call `get-tax-document-by-id` (`GET /client-api/v0/tax-documents/{taxDocId}`); a `404` means the id is unknown or inaccessible to your role.
3. **List its files** — call `get-tax-document-list-files` (`GET /client-api/v0/tax-documents/{taxDocId}/files`).
4. **Download each file** — call `get-tax-document-download-file` (`GET /client-api/v0/tax-documents/{taxDocId}/files/{fileId}/download`).

## Conventions
- Tax documents relate back to a holding / investing entity / issuing entity / account (see data-model/arch-labs-data-model.yml).
- Honor `RateLimit-*` headers; back off on `429`.
