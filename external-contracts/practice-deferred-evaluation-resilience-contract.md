---
slug: practice-deferred-evaluation-resilience-contract
title: Practice Submissions Deferred Evaluation and Score Resilience Contract
description: Contract between practice-service and llm-service for unblocked submissions, deferred scoring
  queue, retry limits, and single score guarantee.
when_to_use: Use when handling student submissions during AI outages, deferred scoring, retry exhaustion,
  or the real SCORE_CALCULATED/SCORE_DEFERRED events published by llm-service on llm.evaluation.results.
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 2
tags:
- contracts
- deferred-evaluation
- grupo-practicas
- kafka
- llm-service
- practice-service
- resilience
---

## Rule

When student submissions are evaluated, no AI model outage, rate limit or transient failure may block student completion. `practice-service` awards base XP with a neutral factor upon attempt submission (RF-IA-27); `llm-service` manages an idempotent, durable deferred evaluation path and is the only source of the pedagogical score. `llm-service` never decides pass/fail, grades, XP or gamification badges â `practice-service` applies the gamification modifier (PAR-05) and awards student progression.

## Resilient Submission Workflow

1. **Attempt closed.** `practice-service` publishes `ATTEMPT_CLOSED` on `practice.events` (provisional topic name).
2. **Evaluator healthy.** `llm-service` evaluates and publishes `SCORE_CALCULATED`.
3. **Evaluator unavailable / invalid response / rubric unavailable.** `llm-service` publishes `SCORE_DEFERRED` instead, and retries internally; it does not ask `practice-service` to resend `ATTEMPT_CLOSED`.
4. **Score completion.** Once evaluated, `llm-service` publishes `SCORE_CALCULATED`. Exactly one final score is ever published per attempt.

## Kafka Events â IMPLEMENTED (verified against `llm-service.asyncapi.yaml` and `AttemptEvaluationService`)

Both are published on the same provisional topic **`llm.evaluation.results`** (key `courseCohortId`), envelope `{eventId, eventType, timestamp, producer, payload}`, no `eventVersion`. The topic mixes both event types: consumers must branch on `eventType`, a single typed payload does not cover both.

### `SCORE_CALCULATED`

```json
{
  "attemptId": "uuid",
  "courseCohortId": "uuid",
  "learnerId": "uuid",
  "rubricVersionId": "uuid",
  "score": 85,
  "dimensions": { "autonomy": 80, "clarity": 90, "progression": 85, "compliance": 95, "efficiency": 75 },
  "evaluator": { "provider": "fake", "model": "fake-evaluator-v1" }
}
```

All seven fields are required. `score` is computed by code from the rubric's fixed weights, not by the model. **This replaces an earlier, incorrect version of this contract** that listed `evaluationId`, `challengeId`, `courseId`, `calibrationRunId`, `completedAt` instead â those fields do not exist on the real event; do not build a consumer against them.

### `SCORE_DEFERRED`

```json
{
  "attemptId": "uuid",
  "courseCohortId": "uuid",
  "learnerId": "uuid",
  "reason": "MODEL_UNAVAILABLE",
  "retryFrom": "2026-09-22T00:15:00Z"
}
```

`reason` is one of `MODEL_UNAVAILABLE`, `INVALID_MODEL_RESPONSE`, `RUBRIC_UNAVAILABLE`. Deferred scores are not retried automatically by `practice-service`: `llm-service` resends `ATTEMPT_CLOSED`-triggered evaluation internally with the same `eventId` semantics; consumers just wait for the eventual `SCORE_CALCULATED`.

## PROPOSED, NOT IMPLEMENTED

- **Retries-exhausted signal.** No event exists yet for "a deferred attempt hit the retry limit" (candidate fact, no topic or eventType assigned â do not use `score_diferido_reintentos_agotados.v1`, that name is retired).
- **Administrative resumption.** `POST /api/llm/admin/deferred-evaluations/{evaluationId}/resumptions` (D-118) does not exist in the current OpenAPI (`llm-service.openapi.yaml`). Proposed shape only: requires `reason`, idempotent, guarantees exactly one final score per attempt.

## Boundaries

- **Pedagogical scoring only.** `llm-service` scores exclusively the five fixed dimensions (0-100). It never decides pass/fail, grades, XP or badges.
- **XP application.** `practice-service` applies PAR-05 (Â±20% XP based on score) and awards progression; `llm-service` never touches XP.