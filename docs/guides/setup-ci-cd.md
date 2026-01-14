---
title: CI/CD Integration
description: Automate formation deployment with GitHub Actions and other CI systems
---
# CI/CD Integration

## Deploy formations automatically on every push

Set up continuous deployment so your formations deploy automatically when you push to main. This guide covers GitHub Actions, but the approach works with any CI system.

## Overview

Deploy formations automatically on push:

```
Git Push → CI/CD → Deploy to MUXI Server
```

## GitHub Actions

### Workflow

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Formation

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install MUXI CLI
        run: curl -fsSL https://muxi.org/install | bash
      
      - name: Configure Profile
        env:
          MUXI_KEY_ID: ${{ secrets.MUXI_KEY_ID }}
          MUXI_SECRET_KEY: ${{ secrets.MUXI_SECRET_KEY }}
          MUXI_SERVER_URL: ${{ secrets.MUXI_SERVER_URL }}
        run: |
          muxi profile add ci \
            --url "$MUXI_SERVER_URL" \
            --key-id "$MUXI_KEY_ID" \
            --secret-key "$MUXI_SECRET_KEY"
      
      - name: Deploy
        run: muxi deploy --profile ci
```

### Secrets

Add to GitHub repository secrets:

| Secret | Description |
|--------|-------------|
| `MUXI_KEY_ID` | Server key ID |
| `MUXI_SECRET_KEY` | Server secret key |
| `MUXI_SERVER_URL` | Server URL |

### Formation Secrets

For formation secrets (`.key`), add as secret and restore:

```yaml
- name: Restore Formation Key
  run: echo "${{ secrets.FORMATION_KEY }}" > .key
```

## GitLab CI

Create `.gitlab-ci.yml`:

```yaml
deploy:
  stage: deploy
  image: ubuntu:latest
  script:
    - curl -fsSL https://muxi.org/install | bash
    - muxi profile add ci --url "$MUXI_URL" --key-id "$MUXI_KEY_ID" --secret-key "$MUXI_SECRET"
    - muxi deploy --profile ci
  only:
    - main
```

Add variables in GitLab CI/CD settings.

## Validation Step

Validate before deploy:

```yaml
- name: Validate Formation
  run: muxi deploy --validate
```

Fails the build if formation is invalid.

## Environment-Specific Deploy

Deploy to different environments:

```yaml
jobs:
  deploy-staging:
    if: github.ref == 'refs/heads/develop'
    steps:
      - run: muxi deploy --profile staging

  deploy-production:
    if: github.ref == 'refs/heads/main'
    steps:
      - run: muxi deploy --profile production
```

## Rollback on Failure

```yaml
- name: Deploy
  id: deploy
  run: muxi deploy --profile production

- name: Rollback on Failure
  if: failure() && steps.deploy.outcome == 'failure'
  run: muxi formation rollback $FORMATION_ID --profile production
```

## Best Practices

1. **Validate first** - Catch errors before deploy
2. **Use secrets** - Never commit credentials
3. **Environment separation** - Different profiles per env
4. **Rollback plan** - Automate failure recovery
5. **Notify on failure** - Alert when deploys fail

## Next Steps

- [Deploy to Production](deploy.md) - Manual deployment
- [Monitoring](monitoring.md) - Post-deploy monitoring
