# Quickstart

This quickstart describes the target workflow for a company adapting the harness.

## 1. Choose Your Company Defaults

Decide:

- internal base domain, for example `internal.example.com`;
- Git provider;
- auth provider;
- reverse proxy;
- runtime host;
- default app port range;
- whether new apps require auth by default.

## 2. Create the Platform Repositories

Recommended repositories:

- `open-company-app-harness`: shared harness, schemas, templates, docs, CLI;
- `company-app-deploy-requests`: GitOps deploy request repository;
- one repository per app.

## 3. Prepare the First App

Copy `templates/app/` into a new app repository and edit:

- `company-app.yaml`;
- `.env.example`;
- `README.md`;
- application code.

Validate locally:

```bash
docker compose config
```

When the CLI is available:

```bash
company-deploy validate ./company-app.yaml
company-deploy checks ./company-app.yaml
```

## 4. Open a Deploy Request

Copy `templates/gitops-request/request.yaml` to the GitOps repository:

```text
requests/example-app.yaml
```

Open a pull request. The PR should validate schemas and policies, but it should not deploy production.

## 5. Merge to Approve

Merging the deploy request is the first deploy approval signal.

The orchestrator should create or update:

- runtime directory;
- local `.env`;
- app record;
- port reservation;
- reverse proxy route;
- auth policy;
- health checks.

## 6. Operate

The portal should show:

- current status;
- last deploy;
- latest health result;
- deploy history;
- useful remediation messages when checks fail.

