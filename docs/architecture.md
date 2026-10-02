# Architecture

Open Company App Harness separates app creation from production deployment.

Coding agents and developers can prepare code, manifests, Docker Compose files, tests, and deploy requests. The harness validates those artifacts and only deploys after a controlled approval path.

## Components

### App Repository

Each app repository contains:

- `company-app.yaml`: operational manifest;
- `docker-compose.yml`: runtime definition;
- `.env.example`: documented environment variables without secrets;
- `README.md`: local development and deployment notes;
- `/health`: HTTP health endpoint.

### GitOps Repository

The GitOps repository stores deploy requests, usually one file per app:

```text
requests/example-app.yaml
```

A pull request validates schema and policy. Merging the request is the approval signal for first deploy.

### Portal Orchestrator

The portal records:

- app inventory;
- deploy attempts;
- check results;
- runtime state;
- health status;
- audit events.

The portal should compare Git intent with real infrastructure state instead of becoming the only source of truth.

### Runtime Host

The runtime host runs Docker Compose apps and local infrastructure integrations, such as:

- reverse proxy;
- auth gateway;
- app containers;
- shared services like Postgres, Redis, or object storage when appropriate.

### Coding Agent Rules

Coding agents should read the live rules before preparing a repo. They may edit app code and open pull requests, but they should not directly create proxy routes, assign fixed ports, SSH into production, or run deployment commands unless explicitly approved.

## Flow

1. A user asks an agent or developer to create an internal app.
2. The app repo is prepared from the template.
3. `company-app.yaml` declares owner, runtime, domain, auth, health, and optional dependencies.
4. Local checks validate the manifest, compose file, and health behavior.
5. A deploy request PR is opened in the GitOps repository.
6. CI validates the request without production secrets.
7. Merge approves the first deploy.
8. The orchestrator creates or updates runtime state.
9. Health and deploy status are recorded in the portal.
10. Future code deploys can happen from the app repo's main branch after the app is onboarded.

