---
name: Look up Corridor PR security reviews
description: Find a Corridor security review for a specific GitHub pull request by number, read the full review and its findings, and handle in-progress scans.
api: openapi/corridor-openapi.yml
operations: [getPrReviewByNumber, getPrReview, listPrReviewFindings]
---

# Look up Corridor PR security reviews

Retrieve Corridor's automated security review for a pull request — useful in
CI/CD to gate merges on unresolved findings.

## Auth
`Authorization: Bearer cor_...` (token from https://app.corridor.dev/settings).

## Steps
1. **Find the review by PR number** — `getPrReviewByNumber`
   (`GET /teams/{id}/pr-reviews/by-pr?prNumber={n}&repoName={repo}`). Add
   `fullHistory=true` to get every complete review for the PR.
   Branch on the response `status` field without parsing error strings:
   - `200` with no `status` → a complete review (or `{ reviews, total }`).
   - `200` `{ "status": "in_progress" }` → a scan is running; poll again.
   - `404` `repo_not_found` → no project matches `repoName`.
   - `404` `pr_review_not_found` → no review or in-progress scan for this PR.
2. **Read a specific review** — `getPrReview`
   (`GET /teams/{id}/pr-reviews/{prReviewId}`) for the full review with comments,
   findings, and metadata (`github_pr_id`, `publish_status`).
3. **List the review's findings** — `listPrReviewFindings`
   (`GET /teams/{id}/pr-review-findings?prReviewId={id}`) with `limit`/`offset`.

## Notes
- Only reviews with `publish_status` `SUCCEEDED` or `SKIPPED` are returned,
  newest first.
- To block merges on findings, turn PR review into a required GitHub status check
  (see https://docs.corridor.dev/features/blocking-prs).
- Errors: `{ "error": "..." }`; `429` = rate limited. See
  conventions/corridor-conventions.yml.
