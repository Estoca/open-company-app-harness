# Security

The harness is designed around a conservative trust boundary.

## Trust Boundary

- Coding agents may edit files and open pull requests.
- Pull requests may run validation without secrets.
- Real deploys happen only after merge or another explicit approval signal.
- Deploy tokens live in runner or orchestrator secrets.
- The orchestrator accepts deploy requests only from trusted automation.

## Rules

- Never store secrets in app manifests or deploy request files.
- Never deploy production on pull request events.
- Never run untrusted pull request code with deploy credentials.
- Never delete databases, volumes, repositories, stacks, or proxy routes automatically.
- Never overwrite an existing domain or app without an explicit policy.
- Prefer authentication for internal apps by default.
- Prefer automatic port allocation for new apps.
- Keep local `.env` files out of Git.

## Required Validations

- Authorized author or merger.
- Repository URL belongs to an approved Git provider or organization.
- Domain belongs to an approved internal base domain.
- App manifest is present and valid.
- Compose file and `.env.example` are present.
- Healthcheck is declared.
- `deploy.port` is `auto`, unless an exception policy approves a fixed port.
- No obvious secret values are present in tracked files.

