# Architecture

Open Company App Harness describes a single-box internal cloud: one machine that runs the core services a small company needs to build, protect, deploy, observe, and operate internal apps.

The deployment harness is only one layer of the system. The full architecture includes ingress, SSO, Git, runtime, shared services, observability, and an agent-friendly control loop.

## Topology

```text
users
  |
cloudflared
  |
reverse proxy
  |
auth gateway
  |
internal apps
  |
shared services
```

The support plane runs beside the app path:

```text
Git server + Actions
Portainer
Glances
App portal / orchestrator
Backups and local config
```

Coding agents and developers prepare code, manifests, Docker Compose files, tests, and deploy requests. The harness validates those artifacts and only deploys after a controlled approval path.

## Components

### Ingress

The ingress layer exposes internal apps without requiring every app to manage TLS, public ports, or edge configuration.

The reference path is:

- Cloudflare Tunnel or another tunnel provider;
- Nginx Proxy Manager, Traefik, Caddy, or nginx;
- one route per app domain.

### Auth Gateway

The auth gateway provides SSO and default access control for internal apps.

The reference implementation uses Authentik with forward-auth headers. Apps can trust the gateway for identity headers and avoid implementing login from scratch unless they need app-specific permissions.

### Git Server

The Git server hosts the harness, app repositories, deploy request repositories, and automation workflows.

The reference implementation uses Gitea because it is small, self-hosted, and enough for internal app workflows.

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

### Operational Agent

In the first practical version of the harness, a terminal-connected coding agent can act as the operational agent for the runtime host.

The operational agent does not replace GitOps approval. It executes the approved change on the machine when the orchestrator is not yet fully automated. Typical responsibilities include:

- reading live host state before making changes;
- preparing ignored local config and runtime directories;
- allocating or confirming ports;
- applying Docker Compose changes;
- creating or updating reverse proxy routes;
- creating or updating auth gateway applications and providers;
- checking container health, HTTP health endpoints, and auth behavior;
- recording the result back in the portal or deploy request.

This matches the early single-box model: the same machine hosts Git, SSO, proxy, runtime, observability, and the agent workspace, so the agent can compare intended state with real state and fix integration issues quickly.

The boundary is important. The agent should only perform production mutations after an explicit approval signal, and it should never silently delete or overwrite repositories, databases, volumes, proxy routes, auth config, or secrets.

### Runtime Host

The runtime host is the same single box. It runs Docker Compose apps and local infrastructure integrations, such as:

- reverse proxy;
- auth gateway;
- app containers;
- shared services like Postgres, Redis, or object storage when appropriate.

### Support Plane

The support plane keeps the single box understandable:

- Portainer shows containers, images, volumes, networks, and stacks.
- Glances exposes host resource usage.
- The portal records inventory, deploy events, health, ownership, and drift.
- Backups and local generated config are explicit operational concerns.

### Coding Agent Rules

Coding agents should read the live rules before preparing a repo. They may edit app code and open pull requests, but they should not directly create proxy routes, assign fixed ports, SSH into production, or run deployment commands unless explicitly approved.

## App Deployment Flow

1. A user asks an agent or developer to create an internal app.
2. The app repo is prepared from the template.
3. `company-app.yaml` declares owner, runtime, domain, auth, health, and optional dependencies.
4. Local checks validate the manifest, compose file, and health behavior.
5. A deploy request PR is opened in the GitOps repository.
6. CI validates the request without production secrets.
7. Merge approves the first deploy.
8. The workflow records the deploy request with the portal/orchestrator.
9. The orchestrator, or an approved operational agent connected to the runtime host, creates or updates runtime state.
10. Health and deploy status are recorded in the portal.
11. Future code deploys can happen from the app repo's main branch after the app is onboarded.

## Why Single Box First

The single-box model works because it optimizes for operational coherence:

- the agent can inspect the same environment it helps operate;
- Git, SSO, proxy, runtime, and observability share one failure domain and one mental model;
- internal apps get real production behavior without needing a full platform team;
- small teams can defer Kubernetes and managed cloud complexity until they actually need it;
- the platform remains portable because the contracts are explicit.
