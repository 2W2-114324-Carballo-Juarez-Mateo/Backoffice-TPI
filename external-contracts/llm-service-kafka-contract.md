---
slug: llm-service-kafka-contract
title: LLM Service Kafka contract
description: Canonical AsyncAPI contract for the Kafka events llm-service publishes and consumes, including
  the attempt evaluation flow with practice-service.
when_to_use: 'Use when consuming llm-service scores, calibration outcomes or moderation incidents, or
  publishing ATTEMPT_CLOSED over Kafka: practice.events, llm.evaluation.results, llm.moderation.events.'
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 6
tags:
- asyncapi
- calibration
- contracts
- evaluator
- events
- kafka
- llm-service
- microservices
- moderation
- practice-service
---

## Rule

Use the attached AsyncAPI document as the canonical Kafka contract for events published and consumed by llm-service. Preserve the five-field envelope, correlation headers, at-least-once delivery and eventId deduplication rules. The envelope follows the platform Kafka standard: exactly `eventId`, `eventType`, `timestamp`, `producer`, `payload`, no `eventVersion`, everything emitted in English.

## Test bot first, real model later

Today the llm-service evaluator is a **test bot** (`fake`): there is no real AI model behind it, the scores are arbitrary (between 55 and 95) and `evaluator` says `fake` and `fake-evaluator-v1`, but the events have the real shape. Its purpose is to let practice-service test the **connection**: publishing `ATTEMPT_CLOSED`, reading the score, duplicates, rejected events and deferred scores. Once that is verified, llm-service switches to the real model without a redeploy and without any change on the practice-service side.

- **What stays the same.** Topics, `eventType`, keys, payload fields and types.
- **What changes.** The score values, `evaluator.provider` and `evaluator.model` (treat them as opaque text), how long an evaluation takes, and how often `SCORE_DEFERRED` appears.
- **What the bot does not validate.** The quality of the scores and of the per-dimension breakdown, and the real evaluation time.

## Attempt evaluation (practice-service)

Publish a closed attempt as one event and read the score from another topic: nobody waits for the evaluation in the same call. llm-service never grants XP, so forward the score to the challenges engine yourself.

- **Bus and topics.** Bus `event-bus:29092` (env var `KAFKA_BOOTSTRAP`). Groups cannot create topics: the notifications group assigns them, so these names are **provisional** â since 2026-09-22 renamed to the `domain.subdomain` dotted style every other team's own document in the platform's messaging-contracts document actually uses (was English-hyphenated before: `practice-events`, `evaluation-events`). `practice.events`: practice-service publishes, llm-service reads with group `llm-service`. `llm.evaluation.results`: llm-service publishes with key `courseCohortId`, practice-service reads with its **own** group (its service name). There is no dead-letter topic.
- **Envelope in the body**, as JSON text, exactly five fields: `eventId`, `eventType`, `timestamp`, `producer`, `payload`. No `eventVersion`. `eventId` and `eventType` are repeated as headers so consumers can filter without parsing.
- **`ATTEMPT_CLOSED` payload.** `attemptId`, `courseCohortId`, `learnerId`, `transcript` (the whole conversation as an array of `{role, content}`). One event per closed attempt, published through an outbox; reuse the same `eventId` when a send is retried.
- **`SCORE_CALCULATED` payload.** `attemptId`, `courseCohortId`, `learnerId`, `rubricVersionId`, `score` (0-100, weighted by code, not by the model), `dimensions` (`autonomy`, `clarity`, `progression`, `compliance`, `efficiency`), `evaluator` (`provider`, `model`, opaque text).
- **`SCORE_DEFERRED` payload.** `attemptId`, `courseCohortId`, `learnerId`, `reason` (`MODEL_UNAVAILABLE`, `INVALID_MODEL_RESPONSE`, `RUBRIC_UNAVAILABLE`), `retryFrom`. Deferred scores are not retried automatically: resend `ATTEMPT_CLOSED` with a new `eventId`.
- **Consumers.** `llm.evaluation.results` mixes two event types with different payloads, so consume `Event<?>` (or the raw JSON body) and branch on `eventType`; dedupe by `eventId`; keep the latest event per `attemptId`. Order is guaranteed only inside one `courseCohortId`. llm-service publishes plain JSON text with no `__TypeId__` header: with Spring's `JsonDeserializer` set `spring.json.use.type.headers=false` and a default type.
- **`timestamp`.** llm-service publishes ISO-8601 text in UTC. Spring's `JsonSerializer` writes an `Instant` as a number, so publish the text form (`@JsonFormat(shape = STRING)`); llm-service does not read the `timestamp` of `ATTEMPT_CLOSED`.
- **Bad input.** A repeated `eventId` is ignored. Missing or non-UUID ids, or a `transcript` that is not an array, are stored in llm-service's `event_dead_letter` table and produce no score.
- **Evolution.** Additive changes only; consumers ignore unknown fields. The standard has no `eventVersion` yet, so an incompatible change is announced in writing and coordinated with consumers (open point with the notifications group).

## Chat moderation (chat-service)

Implemented and real, unlike the sections below. llm-service publishes `MESSAGE_UNBLOCKED` on `llm.moderation.events` (key `courseId`) when a teacher reverses a message block: payload `messageId`, `incidentId`, `courseId`, `userId`, `resolvedBy`, `resolution`, optional `resolutionReason`. `JAILBREAK_INCIDENT_DETECTED` is defined on the same topic but not implemented yet â no payload agreed.

## Proposed alerts toward sistema.notificaciones (owned by the notifications team, not llm-service)

Two events documented as **PROPOSED, not implemented**, because two other teams already reference llm-service as their producer in their own documents:

- `LLM_BUDGET_ALERT` â proposed payload `percentage`, `threshold`, `remainingUsd`, `monthlyCapUsd`, `date`. Requested by the Backoffice team (budget threshold alert for the AI tutor).
- `ALERTA_DESVIACION_IA` â proposed payload `modelId`, `modelVersion`, `deviationScore`, `thresholdMax`, `status`. Requested by the notifications team itself, in their own catalog for llm-service. Open question: whether this replaces or complements `CALIBRATION_OUT_OF_TOLERANCE`, and whether `producer` should stay `llm-service` or switch to the `tema-XX-...` pattern most other teams use.

Neither publishes to a topic llm-service owns; `sistema.notificaciones` belongs to the notifications team.

## Reasoning

This follows the platform Kafka standard (KAFKA.pdf, from the course staff): a five-field envelope with no `eventVersion`, UPPER_SNAKE_CASE event types, topics assigned by the notifications group, bus `event-bus:29092`. Idempotent consumers, the outbox and the correlation headers are not in that standard and do not contradict it, so llm-service keeps them. The topic rename to dotted names is style-only: proposing `practice-events` alongside every other team's `course.lifecycle`/`challenges.results` would just create a second, inconsistent convention on the same bus. Open points with the notifications group: the final topic names, the `producer` value (llm-service publishes `llm-service`), whether `timestamp` is text or a number, and how to version events without `eventVersion`. The flow was verified only against a local broker with the test bot for the attempt-evaluation and moderation sections; the two proposed alerts and the calibration payloads are documentation only, not exercised in tests. The complete guide for the practice-service team (v2) is attached to [[building-the-practice-service-tutor-client-and-score-consumer]].