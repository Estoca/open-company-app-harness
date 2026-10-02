# Agent Instructions

This repository is designed to be operated by terminal-connected coding agents.

If a user asks you to set up this repository on a machine, follow this order:

1. Read `README.md`.
2. Read `docs/agent-setup-contract.md`.
3. Read `docs/llm-bootstrap.md`.
4. Inspect `docker/compose.example.yml`, `scripts/bootstrap.mjs`, `bootstrap/repos.example.json`, and `templates/app/`.
5. Explain the proposed single-box cloud before changing the machine.
6. Ask for missing local values.
7. Run plan mode before apply mode.
8. Ask for explicit approval before starting services, creating public routes, creating DNS records, or changing auth/proxy configuration.
9. Keep generated config and secrets out of Git.
10. Verify the final stack and report what changed.

The target architecture is a single-box internal cloud:

```text
user -> tunnel -> reverse proxy -> auth gateway -> app
```

Supported by:

- Git server and automation;
- Docker Compose runtime;
- app portal / orchestrator;
- Portainer;
- Glances;
- shared services such as Postgres and Redis;
- GitOps deploy requests;
- app manifests and health checks.

Default to safety. Do not delete existing repositories, volumes, databases, proxy routes, or auth config. If the host already contains services, inspect and preserve them by default.
