# GitOps Flow

The GitOps repository controls the first deployment approval for new apps.

## Principle

The Git provider validates who requested a change, which checks passed, and who merged it. The orchestrator deploys with service credentials stored outside chat and outside app repositories.

## Pull Request

Pull requests are for validation and review.

Expected events:

- create or update `requests/<app-slug>.yaml`;
- validate schema;
- validate basic policies;
- comment with actionable errors when validation fails.

A pull request must not perform a production deploy.

## Merge

Merge to the default branch is the approval signal.

Expected events:

- workflow reads changed files under `requests/`;
- workflow calls the orchestrator API with a service token;
- orchestrator creates an auditable deploy record;
- workflow prints a short status and link;
- deploy continues asynchronously.

## Suggested Deploy States

- `queued`
- `validating`
- `building`
- `publishing`
- `healthcheck`
- `deployed`
- `failed`
- `blocked`

The detailed status should live in the portal. The Git provider should receive a compact summary and a link.

