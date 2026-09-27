---
slug: users-service-http-contract
title: users-service HTTP contract
description: Routes, identity, scopes and problem+json error types for callers of users-service.
when_to_use: 'Use when calling users-service over HTTP: login, refresh, registration, profile, whitelist,
  cookies, service scopes, or reading its problem+json errors.'
stack: shared
type: contract
owning_team: Identidad y Usuarios
version: 1
tags:
- contracts
- cookies
- errors
- gateway
- http
- identity
- problem+json
- profile
- scopes
- users-service
---

## Rule

Call users-service only through the API Gateway at `/api/users/**` (anonymous under `/api/users/public/**`), and take its live OpenAPI at `/api/users/public/v3/api-docs` as the canonical endpoint list. Branch on the problem+json `type`, never on the status code.

## Identity

- **People** travel in the `fu_at` HttpOnly cookie (10 min; the refresh lives in `fu_rt`, 7 days, rotated on every use). A person token in `Authorization` is rejected. The person flow is `POST /api/users/public/auth/login` then `POST /api/users/public/auth/2fa/verify`; `/auth/refresh` is public because the refresh token travels in the cookie.
- **Services** travel in `Authorization: Bearer`, obtained with `client_credentials` from `POST /api/users/public/auth/token`. `audience` is mandatory and the scope must be in users-service's catalogue (`users.profile.read`, `market.catalog.read` today). One token per destination.
- The Gateway injects the `X-*` identity headers; users-service trusts them because it publishes no ports. Never send them yourself.

## What other services call today

`GET /api/users/profile/{id}` returns a public profile (names, GitHub username, avatar; no email, legajo or account status) to a person or to a service token holding `users.profile.read`. JWKS is at `/.well-known/jwks.json`, outside `/api`.

## Errors and gates

- Every error is `application/problem+json`. On top of the Gateway types, users-service adds `invalid-code`, `invalid-link`, `invalid-link-state`, `provider-not-supported`, `provider-not-linked`, `provider-already-linked`, `provider-account-taken`, `provider-unavailable`, `pending-account`, `password-change-required`, `onboarding-pending`, `email-not-whitelisted`, `access-denied`, `last-admin`, `invalid-transition`, `duplicate-email`, `too-many-attempts`.
- A person with a valid token can still get `403 pending-account`, `password-change-required` or `onboarding-pending` on private routes (the account gates). `GET /me` is exempt from all three so a client can explain the block.
- Password reset, activation resend and a bad link or code answer the same whether the account exists or not (anti-enumeration): never read them as "the email is registered".
- Rate limits: the Gateway caps per IP on `/api/users/public/auth/**` and `/registration/**`; the service caps per email (login failures, reset requests, 2FA challenges, resends) with `429 too-many-attempts` plus `Retry-After`.

## Operational notes

Gateway timeout is 3 s and retry is GET-only. Session state is cached for 3 seconds: right after login or logout the previous state can still be accepted, so wait ~4 s before concluding anything. The exhaustive endpoint inventory (DTOs, roles, statuses) lives in the OpenAPI, not here on purpose.