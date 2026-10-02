# App Contract

`company-app.yaml` is the operational contract for a deployable internal app.

## Minimal Deployable App

```yaml
schema_version: 1

app:
  slug: example-app
  name: Example App
  owner_email: owner@example.com
  description: Example internal app.

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

database:
  required: false
  engine: postgres
  migrations_command: npm run migrate

object_storage:
  required: false
  provider: s3-compatible
  bucket: auto

dependencies:
  redis:
    required: false
    shared: true
    prefix: example-app:

checks:
  build_command: docker compose build
  test_command: npm test
```

## Required Rules

- `schema_version` must be `1`.
- `app.slug` should use lowercase letters, numbers, and hyphens.
- `app.owner_email` is required.
- Deployable apps should use `runtime.type: docker-compose`.
- Deployable apps must declare a compose file and primary service.
- Deployable apps must declare an HTTP healthcheck.
- Deployable apps must declare a domain.
- New apps should use `deploy.port: auto`.
- `.env.example` must exist and must not contain real secrets.
- Internal apps should use `auth.required: true` by default.
- Apps requiring a database must declare a migration command.

## Non-Deployable Repository

Documentation or contract-only repositories can declare:

```yaml
runtime:
  type: documentation

deploy:
  enabled: false
  reason: No deployable app.
```

