# Example App

This app follows the Open Company App Harness contract.

## Local Development

```bash
cp .env.example .env
docker compose up --build
```

Healthcheck:

```bash
curl http://localhost:8080/health
```

## Deployment

Before requesting deploy, validate:

```bash
docker compose config
```

When the harness CLI is available:

```bash
company-deploy validate ./company-app.yaml
company-deploy checks ./company-app.yaml
```

Open a deploy request in the GitOps repository using `templates/gitops-request/request.yaml`.

