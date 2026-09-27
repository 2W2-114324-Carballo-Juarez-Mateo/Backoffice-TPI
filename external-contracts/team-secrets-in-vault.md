---
slug: team-secrets-in-vault
title: Team secrets in Vault
description: Where a team's microservice secrets live in Vault, how the Agent delivers them, and how to
  store, rotate and never leak them.
when_to_use: 'Use when a microservice needs a secret from Vault: onboarding a team AppRole, storing or
  rotating a DB password or service client secret, app.env, config tree, or Vault down at startup.'
stack: infra
type: convention
owning_team: Identidad y Usuarios
version: 1
tags:
- app-env
- approle
- config-tree
- credentials
- rotation
- secrets
- security
- vault
---

## Rule

Every secret a team's microservice needs lives in Vault under `secret/tpi/<team>/`, never in the repo, the compose file, the image or a chat. The micro does not talk to Vault: a Vault Agent authenticates with the team's AppRole, renders `secret/tpi/<team>/env` to `/run/secrets/app.env`, and the micro imports that file as a Spring config source.

## What lives where

| Path | Who | Content |
|---|---|---|
| `secret/tpi/<team>/env` | the team, read/write | one key per variable: DB password, API keys, `CLIENT_ID`/`CLIENT_SECRET`, launch parameters |
| `secret/tpi/shared/clients/<team>` | written by Identity, read by the team | the service client secret for calling other micros |
| `secret/tpi/identity/*` | Identity only | JWT signing keys, bootstrap and DB secrets |

Never store a secret anywhere else. `.env` on the server is the pre-Vault fallback, not a second source.

## One-time onboarding

Identity runs `scripts/vault-onboard-team.sh <team> [member ...]` and hands over, by a private channel: `VAULT_ADDR`, `role_id`, a **wrapping token (10 minutes, single use)**, `ca.pem` and `agent.hcl`. The team unwraps once:

```bash
mkdir .vault && cd .vault          # add .vault/ to .gitignore now
printf %s '<role_id>' > role_id
VAULT_ADDR=<addr> VAULT_CACERT=./ca.pem vault unwrap -field=secret_id <wrapping-token> > secret_id
```

Unwrap within 10 minutes; a new wrapping token is also how `secret_id` is rotated. `.vault/` never reaches the repo.

## Store and rotate

```bash
vault kv put   -mount=secret tpi/<team>/env SPRING_PROFILES_ACTIVE=prod DB_PASSWORD=...
vault kv patch -mount=secret tpi/<team>/env DB_PASSWORD=new-value   # change ONE key
```

A backslash in a value must be doubled (`\\`); `$` is safe as-is, only `${` is special once Spring resolves placeholders. The Agent re-renders within about a minute; a running process read its config at startup, so **restart the service** to pick up a new value. If Vault is down, an already-running service keeps working; one that must start needs Vault reachable.

## Use them in the micro

The team compose sets `SPRING_CONFIG_IMPORT=optional:file:/run/secrets/app.env[.properties]`; keys are then read as `${KEY}` placeholders, the same way `${DB_URL}` already is. A properties file is not a shell environment: a key there does not become an OS env var, and `SPRING_PROFILES_ACTIVE` does not bind to `spring.profiles.active` on its own. Keep `optional:` so an IDE run without Vault still starts. Binding, defaults and fail-fast validation rules live in [[typed-configuration-env-vars-and-secret-files]].

## Never

- Never commit a secret, log it, or paste it into an issue or a chat.
- Never `docker compose down -v` on the Vault projects: it deletes the unseal key (Vault becomes unrecoverable) or the Tailscale node state.
- Never read `secret/tpi/identity/*` or another team's namespace.