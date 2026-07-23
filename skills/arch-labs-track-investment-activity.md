---
name: Track investment activity in Arch
description: List processed investment activities, inspect one, and download its source documents to monitor capital calls, distributions and statements.
api: openapi/arch-labs-client-api-openapi.json
operations:
- get-list-activities
- get-single-activity
- get-list-activity-files
- get-activity-download-single-file
---

# Track investment activity in Arch

Arch turns incoming documents (account statements, capital calls, distribution notices, investor letters) into structured Activities with extracted financial facts. Use this flow to monitor them.

## Prerequisites
- Authenticate: `POST /client-api/v0/auth/token` → `authorization: Bearer <token>`.
- Base URL: `https://arch.co/client-api/v0`.

## Steps
1. **List activities** — call `get-list-activities` (`GET /client-api/v0/activities`). Paginates as Pages; a `500` is a transient server error — retry with backoff.
2. **Inspect an activity** — call `get-single-activity` (`GET /client-api/v0/activities/{id}`). An activity links to its holding, investing/issuing entity, account, task, tax document and cash flow (via *Ref fields). `400` = unparseable id; `404` = not found/inaccessible.
3. **List the activity's files** — call `get-list-activity-files` (`GET /client-api/v0/activities/{id}/files`).
4. **Download a source file** — call `get-activity-download-single-file` (`GET /client-api/v0/activities/{activityId}/files/{fileId}/download`).

## Conventions
- Cash-flow amounts are decomposed into allocations with a direction (-1 in, 1 out); see conventions/arch-labs-conventions.yml and data-model/arch-labs-data-model.yml.
- All timestamps are UTC. Respect `RateLimit-*` headers; `429` means slow down.
