---
name: Triage Corridor security findings
description: Search a team's security findings, inspect a specific finding, and update its state (close as false positive, risk accepted, or fixed) using the Corridor API.
api: openapi/corridor-openapi.yml
operations: [searchFindings, getFinding, updateFinding]
---

# Triage Corridor security findings

Use the Corridor REST API to review and resolve security findings for a team.

## Auth
All calls use `Authorization: Bearer cor_...` (an API token from
https://app.corridor.dev/settings). Tokens carry the creating user's access and
cannot perform admin operations.

## Steps
1. **Search open findings** — call `searchFindings` with `teamId` (required).
   Filter with `state=open`, `severity`, `search`, and `createdAfter`/`createdBefore`
   (ISO-8601). `limit` defaults to 10, max 50.
   `GET /findings/search?teamId={teamId}&state=open&limit=20`
2. **Inspect a finding** — call `getFinding` with the finding `id` to get the full
   record (related project, rule, scan, and PR-review data), including `cwe`,
   `affectedFile`, and `severity`.
   `GET /findings/{id}`
3. **Resolve it** — call `updateFinding` (`PUT /findings/{id}`) with only the fields
   you want to change. To close: `state=closed` plus `closedReasonCategory` one of
   `false_positive | risk_accepted | vulnerability_fixed | other`, and a
   `closedReason` string. To reopen: `state=open`.

## Conventions & errors
- Findings are scoped by team; always pass a valid `teamId`.
- Errors return `{ "error": "..." }`; `401` = bad token, `403` = insufficient
  permission, `429` = rate limited (wait and retry). See
  errors/corridor-problem-types.yml and conventions/corridor-conventions.yml.
