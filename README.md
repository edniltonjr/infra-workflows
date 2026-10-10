# infra-workflows

Workflows reutilizáveis do backstage. Este repositório precisa ser **público** para que
repositórios privados das duas contas possam chamá-lo.

## `deploy.yml`

testes → build da imagem → push para `ghcr.io/<owner>/<repo>:<sha>` e `:latest` →
SSH na VPS de aplicações → grava `docker-compose.yml` e `.env` em `DEPLOY_PATH` →
`docker compose pull && docker compose up -d --wait`.

| Input | Padrão | |
|---|---|---|
| `app-name` | (obrigatório) | slug do app |
| `domain` | `""` | só para o resumo |
| `deploy-path` | `/opt/apps/<app-name>` | pasta na VPS |
| `ssh-port` | `22` | |
| `run-tests` | `true` | |
| `node-version` | `24` | |
| `install-command` / `test-command` | `npm ci` / `npm test` | |
| `compose-file` / `docker-context` | `docker-compose.yml` / `.` | |

Secrets: `DEPLOY_HOST`, `DEPLOY_USER`, `DEPLOY_SSH_KEY`, `DEPLOY_KNOWN_HOSTS` (obrigatórios),
`DATABASE_URL`, `REDIS_URL`, `APP_ENV` (opcionais). O backstage grava todos no repositório do app.

O chamador precisa de `permissions: { contents: read, packages: write }`.

## Versionamento

Os apps chamam `@v1`. Para publicar uma mudança compatível, mova a tag:

```sh
git tag -f v1 && git push -f origin v1
```

Mudanças incompatíveis vão para `v2`.
