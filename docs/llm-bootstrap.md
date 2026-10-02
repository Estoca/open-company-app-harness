# LLM Bootstrap Playbook

This playbook is for a terminal-connected LLM that needs to recreate a single-box company cloud from scratch.

The goal is not to hide infrastructure behind a magic script. The goal is to give the agent a safe, inspectable path:

1. ask the human for the few decisions that cannot be inferred;
2. clone the platform repositories listed in a manifest;
3. generate local configuration files that are not committed;
4. prepare the core stack around Git, SSO, proxy, observability, and apps;
5. produce an execution plan;
6. apply only the steps the human explicitly approves.

## Agent Rules

- Read this file before running bootstrap commands.
- Do not invent production domains, admin emails, or credentials.
- Never commit generated secrets or local config.
- Prefer `--plan` first.
- Use `--apply` only after the human confirms the generated plan.
- Use `--start` only when the human approves starting local services.
- Do not delete existing repositories, databases, volumes, proxy routes, or auth config.
- If a repository already exists locally, fetch status and continue instead of replacing it.

## Prerequisites

The target machine should have:

- Git;
- Node.js 20 or newer;
- Docker and Docker Compose, if the runtime stack will be started;
- network access to the company's Git provider;
- credentials configured through the local Git credential helper, SSH agent, or provider CLI.

## Inputs The Agent Must Collect

The bootstrap script asks for:

- workspace directory for cloned repositories;
- Git base URL;
- Git organization or owner;
- internal base domain;
- portal domain;
- auth provider type;
- initial admin email;
- initial admin password;
- repository manifest path.

The admin password is written only to local generated files. It must not appear in chat, logs, manifests, or committed files.

The agent should also confirm which core services the human wants on the first box:

- tunnel provider, such as Cloudflare Tunnel;
- reverse proxy, such as Nginx Proxy Manager;
- SSO provider, such as Authentik;
- Git server, such as Gitea;
- container manager, such as Portainer;
- host monitor, such as Glances;
- shared databases, caches, or object storage.

## Repository Manifest

The manifest tells the agent which repositories to clone.

Start from:

```text
bootstrap/repos.example.json
```

Then create a local copy:

```bash
cp bootstrap/repos.example.json local/repos.json
```

Edit `local/repos.json` with the real company repositories.

## Bootstrap Flow

Generate a plan:

```bash
node scripts/bootstrap.mjs --plan --repos local/repos.json
```

Run a non-interactive smoke test with defaults:

```bash
node scripts/bootstrap.mjs --plan --defaults --repos bootstrap/repos.example.json
```

Apply safe filesystem and Git steps:

```bash
node scripts/bootstrap.mjs --apply --repos local/repos.json
```

Start local services only after review:

```bash
node scripts/bootstrap.mjs --apply --start --repos local/repos.json
```

## What The Script Generates

The script writes local files under `local/`, which is ignored by Git:

```text
local/bootstrap.env
local/bootstrap-plan.md
local/repos.json
```

`local/bootstrap.env` is a local machine file. It may contain secrets.

`local/bootstrap-plan.md` is safe to inspect before applying, but treat it as local operational context.

## Expected End State

After a successful bootstrap, the machine should have:

- all required repositories cloned under the chosen workspace directory;
- local harness configuration generated;
- a clear next-step plan for starting the single-box cloud services;
- an ingress path design from tunnel to proxy to auth to app;
- a Git server plan for harness, apps, and deploy requests;
- an SSO plan for protecting internal apps;
- an observability plan for host and containers;
- a sample app template available;
- a GitOps repository ready for deploy request files;
- enough context for the agent to continue setup from repository READMEs.

## Human Checkpoints

The agent should stop for human confirmation before:

- starting Docker services;
- creating public DNS records;
- changing reverse proxy routes;
- creating auth applications/providers;
- creating service tokens;
- publishing repositories;
- rotating, deleting, or overwriting anything.
