---
name: Manage Corridor project guardrails
description: List, create, and AI-generate the security guardrails (reports) that Corridor enforces on a project during real-time analysis and PR review.
api: openapi/corridor-openapi.yml
operations: [listGuardrails, createGuardrail, generateGuardrail]
---

# Manage Corridor project guardrails

Guardrails are per-project security rules Corridor enforces during code reviews
and real-time AI-coding analysis. They are managed as project "reports."

## Auth
`Authorization: Bearer cor_...` (token from https://app.corridor.dev/settings).

## Steps
1. **List current guardrails** — `listGuardrails` (`GET /projects/{id}/reports`)
   returns all guardrails (reports) and rulesets attached to the project. Each
   report has `name`, `guardrail` (rule text), and `type` (`guardrail` or `context`).
2. **Create a guardrail explicitly** — `createGuardrail`
   (`POST /projects/{id}/reports`) with required `name` and `guardrail` text, and
   optional `type` (defaults to `guardrail`). Example: name "No hardcoded secrets",
   guardrail "Never commit API keys, passwords, or secrets directly in source code."
3. **Generate a guardrail with AI** — `generateGuardrail`
   (`POST /projects/{id}/guardrails/generate`) with a plain-language `description`
   (max 1000 chars). Returns a `taskId` to track generation progress.

## Notes
- Guardrails are surfaced to AI coding agents via the Corridor MCP `analyzePlan`
  and `getGuardrails` tools (see mcp/corridor-mcp.yml).
- Custom guardrails require a Team or Enterprise plan.
- Errors return `{ "error": "..." }`; see conventions/corridor-conventions.yml.
