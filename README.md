# Open Company App Harness

A lightweight, open source harness for creating, validating, and deploying internal company apps with coding agents, GitOps, Docker Compose, authentication, and health checks.

The project is designed for teams that want AI coding agents to build useful internal tools without turning production deployment into a collection of one-off scripts, copied workflows, and undocumented server state.

## What This Is

Open Company App Harness is a reference architecture and starter kit for:

- defining an operational contract for each app with `company-app.yaml`;
- validating app repositories before deployment;
- using GitOps for first-time deployment approval;
- running apps with Docker Compose;
- integrating with an internal Git server, reverse proxy, auth provider, and app portal;
- giving coding agents clear rules for how to prepare apps safely.

It is not a hosted platform. It is a set of conventions, schemas, templates, and implementation building blocks that a company can adapt to its own infrastructure.

## Core Idea

Every deployable app carries its operational contract in the repository:

```yaml
schema_version: 1

app:
  slug: example-app
  name: Example App
  owner_email: owner@example.com

runtime:
  type: docker-compose
  compose_file: docker-compose.yml
  service: app
  healthcheck:
    type: http
    path: /health
    expected_status: 200

deploy:
  enabled: true
  domain: example-app.internal.example.com
  port: auto
  env_file: .env
  env_example: .env.example

auth:
  required: true
  provider: forward-auth
  allowed_domains:
    - example.com
```

The harness validates that contract, checks the repository, and coordinates deployment through a controlled path.

## Suggested Stack

The reference stack assumes:

- Git server: Gitea, GitHub Enterprise, GitLab, or GitHub;
- reverse proxy: Nginx Proxy Manager, Traefik, Caddy, or nginx;
- auth: Authentik, Authelia, OAuth2 Proxy, or another forward-auth provider;
- runtime: Docker Compose;
- portal/orchestrator: an internal app that records apps, deploys, checks, health, and events.

These tools are intentionally replaceable. The contract matters more than the exact vendor.

## Repository Layout

```text
docs/                  Architecture, quickstart, GitOps flow, security
schemas/               JSON schemas for manifests and deploy requests
templates/app/         Starter files for a deployable internal app
templates/gitops-request/
examples/minimal-web-app/
docker/                Example compose for a local harness stack
packages/cli/          Placeholder for validation and workflow tooling
```

## Status

Early extraction. The first goal is to make the pattern easy to understand, install, and audit. The second goal is to provide a working CLI and a runnable local demo.

## Safety Principles

- Git is the source of truth for app intent.
- Pull requests validate without production secrets.
- Real deployment happens only after an explicit approval signal.
- App manifests never contain secret values.
- New apps use automatic port allocation by default.
- Internal apps should require authentication by default.
- The harness never deletes databases, volumes, repositories, stacks, or proxy routes automatically.

## Getting Started

Start with the docs:

- `docs/architecture.md`
- `docs/quickstart.md`
- `docs/app-contract.md`
- `docs/gitops-flow.md`
- `docs/security.md`

Then inspect:

- `templates/app/`
- `examples/minimal-web-app/`

