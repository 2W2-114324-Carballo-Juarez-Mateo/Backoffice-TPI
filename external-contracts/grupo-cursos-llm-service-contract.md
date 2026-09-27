---
slug: grupo-cursos-llm-service-contract
title: Grupo Cursos and LLM Service Integration Contract
description: Canonical contract between Grupo Cursos (courses-service) and llm-service for course calibration
  status, active courses sync, and deferred evaluations.
when_to_use: 'Use when integrating Grupo Cursos (courses-service) with llm-service: the real active-courses
  Kafka sync, or the proposed calibration-status and deferred-evaluation-summary endpoints.'
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 2
tags:
- calibration
- contracts
- courses-service
- gateway
- grupo-cursos
- kafka
- llm-service
---

## Rule

Interactions between Grupo Cursos (`courses-service`) and `llm-service` split into one real, implemented direction and several proposals presented below as such â do not assume the proposals are live. Synchronous calls route through the API Gateway (`/api/llm/**`) with M2M JWT (`aud=llm-service`), `traceparent`, `X-Request-Id`. Asynchronous events use the platform's five-field envelope (`eventId`, `eventType`, `timestamp`, `producer`, `payload`, no `eventVersion`) and outbox pattern.

## PROPOSED, NOT IMPLEMENTED â HTTP Endpoints (D-81, D-115, D-164)

Neither of these exists in `llm-service.openapi.yaml` today:

### Course Calibration Status
`GET /api/llm/courses/{courseId}/calibration-status` â proposed response shape:
```json
{ "registered": true, "calibrationValidity": "VALID", "evaluatorAvailability": "AVAILABLE", "effectiveCalibrationRunId": "uuid | null" }
```
`calibrationValidity`: `NO_ACTIVE`, `VALID`, `VERIFICATION_PENDING`, `RECALIBRATION_REQUIRED`, `SECURITY_SUSPENDED`. `evaluatorAvailability`: `AVAILABLE`, `UNAVAILABLE`. Would not return rubrics, Golden Sets, skills or prompts.

### Deferred Evaluation Summary for Course Closing
`GET /api/llm/courses/{courseId}/deferred-evaluation-summary` â proposed response shape:
```json
{ "pendingCount": 12, "oldestPendingAt": "2026-09-21T18:20:00Z | null", "retryExhaustedCount": 1, "hasRetryExhausted": true, "updatedAt": "2026-09-21T18:25:00Z" }
```
If built, the rule would be: `courses-service` blocks closing course actas while `pendingCount > 0` or `hasRetryExhausted = true`, with no administrative override. Would never leak student IDs, attempt details or transcripts.

## Asynchronous Kafka

### Active Courses and Calibration Requirements Sync â proposed fact, no topic assigned (D-82 to D-85, D-98)

Producer `courses-service`, consumer `llm-service`. No topic or eventType name is assigned yet â groups cannot create topics, the notifications team does. Proposed payload: resource type `COURSE`, `courseId`, `requiresCalibration` (boolean), a visible display-name snapshot, and a snapshot version. Would be consumed idempotently by `eventId`; `llm-service` would store only the bounded snapshot, never project the full course catalog.

### Calibration Lifecycle Notifications â PROPOSED, no topic assigned (D-165)

Two facts, not implemented, with no topic name (the retired names `calibracion_activada.v1` / `calibracion_suspendida.v1` should not be used â they predate the platform's Kafka standard and no replacement name has been assigned): an activation created/replaced (`activationId`, `scope=COURSE`, `courseId`, `calibrationRunId`), and an activation suspended (`reasonCode` added). Producer would be `llm-service`.

## Boundaries and Responsibilities

- `courses-service` is the sole owner of course lifecycle, the active courses catalog, and teacher course authorizations (D-150).
- `llm-service` never accesses the courses database directly and does not govern course publishing.

## Open point

Both HTTP endpoints and both Kafka facts above need approval from `courses-service` and, for the topic names, from the notifications team, before any of this is implementable.