---
name: Ingest a new fund investment into Arch
description: Create a fund holding for an investing entity and attach its statement and cash-flow documents so Arch can process the activity.
api: openapi/arch-labs-client-api-openapi.json
operations:
- create-funds-holding
- post-holding-statement-upload
- post-holding-cashflow-upload
- get-list-holding-file-types
---

# Ingest a new fund investment into Arch

Use the Arch Client API to record a new fund investment (a Holding representing an Investing Entity's stake in an Issuing Entity's Offering) and upload its supporting documents.

## Prerequisites
- Obtain a JWT: `POST /client-api/v0/auth/token` with `{ "clientId": "...", "clientSecret": "..." }` (credentials from api-support@arch.co). Send it on every call as `authorization: Bearer <token>`. Reuse the token until its `exp`.
- Base URL: `https://arch.co/client-api/v0`.

## Steps
1. **Create the fund holding** — call `create-funds-holding` (`POST /client-api/v0/holdings/fund`) with the investing entity, issuing entity/offering and financial details. A `400` means a required field is missing (see the `{"message"}` body); `500` is a server error — retry with backoff.
2. **Check accepted file types** (optional) — call `get-list-holding-file-types` (`GET /client-api/v0/holdings/{holdingId}/files/types`) to learn which document categories the holding accepts.
3. **Upload the account statement** — call `post-holding-statement-upload` (`POST /client-api/v0/holdings/{id}/files/statements`) with the statement file so Arch can parse valuations.
4. **Upload cash-flow documents** — call `post-holding-cashflow-upload` (`POST /client-api/v0/holdings/{id}/files/cash-flows`) for capital-call / distribution notices. A `404` means the holding id is not found or is inaccessible to your user role.

## Conventions
- Errors are `{"message": "..."}` JSON or `text/plain`; there is no `Idempotency-Key` header, so guard against duplicate creates yourself.
- Respect rate limits: watch `RateLimit-Remaining`/`RateLimit-Reset`; a `429` means slow down.
