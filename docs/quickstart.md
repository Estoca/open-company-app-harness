# Quickstart

This quickstart describes the target workflow for a company adapting the harness as a single-box internal cloud.

## 1. Choose Your Box And Company Defaults

Decide:

- host machine and operating system;
- internal base domain, for example `internal.example.com`;
- Git provider;
- auth provider;
- reverse proxy;
- tunnel or ingress provider;
- default database and cache services;
- default app port range;
- whether new apps require auth by default.

## 2. Start The Core Stack

The reference core stack is:

- Cloudflare Tunnel or another tunnel;
- reverse proxy;
- Authentik or another SSO gateway;
- Gitea or another Git server;
- Portainer;
- Glances;
- app portal / orchestrator;
- optional Postgres, Redis, and object storage.

Use `docker/compose.example.yml` as a starting point, not as a production-ready secret file.

## 3. Create the Platform Repositories

Recommended repositories:

- `open-company-app-harness`: shared harness, schemas, templates, docs, CLI;
- `company-app-deploy-requests`: GitOps deploy request repository;
- one repository per app.

## 4. Publish Agent-Facing App Rules

Before agents create apps, publish one live rules file that explains the company's current app contract.

Use `docs/app-rules-example.md` as a starting point. Adapt it for:

- supported Git providers;
- official app template;
- manifest and Compose requirements;
- auth provider;
- shared database/cache/storage policy;
- CI/deploy workflow;
- first-deploy GitOps process;
- actions that require human approval.

The app portal can serve the rules as Markdown and JSON:

```text
GET /api/agent/rules.md
GET /api/agent/rules.json
```

Tell coding agents to read the live rules before copying patterns from existing app repositories.

## 5. Prepare the First App

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

## 6. Open a Deploy Request

Copy `templates/gitops-request/request.yaml` to the GitOps repository:

```text
requests/example-app.yaml
```

Open a pull request. The PR should validate schemas and policies, but it should not deploy production.

## 7. Merge to Approve

Merging the deploy request is the first deploy approval signal.

The orchestrator should create or update:

- runtime directory;
- local `.env`;
- app record;
- port reservation;
- reverse proxy route;
- auth policy;
- health checks.

## 8. Operate The Box

The portal should show:

- current status;
- last deploy;
- latest health result;
- deploy history;
- useful remediation messages when checks fail.

Portainer should answer container and stack questions. Glances should answer host health questions. The Git server should answer what changed. Authentik should answer who can access what.
