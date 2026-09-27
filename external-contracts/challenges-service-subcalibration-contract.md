---
slug: challenges-service-subcalibration-contract
title: Challenges Service and Exam Subcalibration Boundary Contract
description: Contract between Grupo DesafÃ­os (challenges-service) and llm-service governing challenge
  subcalibration status, catalog ownership, and exam eligibility.
when_to_use: Use when evaluating whether to expose challenge subcalibration status; today there is no
  direct llm-service/challenges-service integration, this is a proposal only.
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 2
tags:
- challenges-service
- contracts
- exams
- gateway
- grupo-desafios
- llm-service
- subcalibration
---

## Rule

**There is no direct integration today between `challenges-service` and `llm-service`.** Since a 2026-09-13 architecture decision, all evaluator traffic is mediated through `practice-service`: `challenges-service` does not call `llm-service`, and `llm-service` does not call `challenges-service`. This document describes a **proposed** endpoint (D-81, D-164), not a contract in force and not an authorized integration. If it is ever exposed, who consumes it still needs to be agreed â it is not assumed to be `challenges-service` directly, precisely because that would contradict the standing boundary decision.

## HTTP Endpoint â PROPOSED, NOT IMPLEMENTED (D-81, D-164)

`GET /api/llm/courses/{courseId}/challenges/{challengeId}/calibration-status` does **not** exist in the executable contract (`llm-service.openapi.yaml`) as of this writing. Proposed shape, not a closed contract:

```json
{
  "registered": true,
  "calibrationValidity": "VALID",
  "evaluatorAvailability": "AVAILABLE",
  "effectiveCalibrationRunId": "uuid | null"
}
```

- `registered: false` would mean llm-service has no subcalibration requirement or active record for this challenge.
- `calibrationValidity`: `NO_ACTIVE`, `VALID`, `VERIFICATION_PENDING`, `RECALIBRATION_REQUIRED`, `SECURITY_SUSPENDED`.
- `evaluatorAvailability`: `AVAILABLE`, `UNAVAILABLE`.
- Would return no rubrics, Golden Sets, skills or prompt data.

## Catalog and Lifecycle Ownership Rules (agreed, independent of the endpoint above)

These hold regardless of whether the proposed endpoint is ever built, because they describe today's boundary, not a new integration:

- `challenges-service` is the sole source of truth for challenges and exams (D-23, D-24, D-80). `llm-service` does not maintain a database projection, status or expiration of challenges, and never accesses the `challenges-service` database directly.
- `challenges-service` does not govern challenge enablement based on AI calibration: gatekeeping is decoupled, each consumer enforces its own publish gates.
- If subcalibration is ever exposed, only challenges published and active in `challenges-service` would be eligible; exams (type `EXAM`) would follow the same rules as regular challenges.
- Neither service infers state from the absence of records; any future check would be explicit and correlated via standard headers (`traceparent`, `X-Request-Id`).

## Open point

Before building the endpoint above: confirm with the platform's architecture owners whether the 2026-09-13 no-direct-contact decision still holds, and if a subcalibration signal is needed, whether it should go through `practice-service` (today's mediator) instead of a new direct channel to `challenges-service`.