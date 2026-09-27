---
slug: llm-service-http-contract
title: LLM Service HTTP contract
description: Canonical OpenAPI contract for synchronous communication with llm-service, including the
  tutor contract for practice-service.
when_to_use: 'Use when integrating practice-service, courses-service or admin-service with llm-service
  over HTTP: the AI tutor, Idempotency-Key, unavailable state, rubrics, Golden Sets or calibration runs.'
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 5
tags:
- admin-service
- calibration
- contracts
- courses-service
- gateway
- http
- llm-service
- microservices
- openapi
- practice-service
- tutor
---

## Rule

Use the attached OpenAPI document as the canonical HTTP contract for synchronous integrations with llm-service. Route every request through the API Gateway and follow its authentication, correlation, idempotency and Problem Details rules.

## Test bot first, real model later

Today llm-service answers with a **test bot** (`fake`): there is no real AI model behind it, the tutor text is a template, but the shape of every request, response and error is the real one. Its purpose is to let practice-service test the **connection**: route, token, headers, errors and idempotency. Once practice-service has verified that, llm-service switches to the real model without a redeploy and without any change on the practice-service side.

- **What stays the same.** Route, headers, scope, request and response fields, status codes and the error body.
- **What changes.** The text of the tutor answers, the latency, and how often `state: unavailable` appears.
- **What the bot does not validate.** The quality of the answers, the real latency and how often the model fails. The bot never returns `unavailable`: ask the llm-service team to force it.

## Tutor (practice-service)

`POST /api/llm/tutor/interactions`. The test bot and the real model share this exact contract: only the content of the answers changes, never their shape.

- **Token.** `client_credentials` with `audience: llm-service` and scope `llm.tutor.interact`. The Gateway adds the `X-*` identity headers: never send or trust them yourself.
- **Headers.** `Authorization: Bearer <token>` and `Idempotency-Key` (a new UUID for every student message).
- **Request.** Required: `attemptId`, `challengeId`, `courseCohortId`, `learnerId` (UUIDs), `message` (not blank), `riskLevel` (`low`, `medium`, `high`). Optional: `conversacionId` (groups turns) and `expectedSolution` (used in memory by the output guard only: never store, log or return it). Unknown fields are ignored.
- **Response `200`.** `message`, `state` (`completed`, `blocked`, `unavailable`), `conversacionId`. Any model failure (timeout, invalid answer, provider down, budget exhausted) returns `200` with `state: unavailable` and a fixed notice. `blocked` is reserved and not produced today, but handle it.
- **Errors** (`application/problem+json`). `401` wrong service or scope; `403` missing delegated identity; `409` same key while the first request is still running; `422` invalid body, or a key reused with a different body. Treat any other 4xx or 5xx as a failure.
- **Idempotency.** Same key and same body return the same response without a new model call. An `unavailable` response is stored under its key, so retry with a **new** key.
- **Timeout.** Worst case is about 25 s (three attempts of 8 s): use a 30 s client timeout.

## Reasoning

Turning every model failure into `200 unavailable` gives the student something to see and keeps the idempotency key from being stuck in progress. Open point: whether the Gateway adds the delegated user for a pure service token; a `403` with a valid token means it does not, so confirm it with the Gateway team. Other endpoints in the file serve courses-service and admin-service. The complete guide for the practice-service team is attached to [[building-the-practice-service-tutor-client-and-score-consumer]].