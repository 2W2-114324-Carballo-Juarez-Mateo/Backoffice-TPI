---
slug: calibration-lifecycle-and-skill-artifacts-contract
title: Calibration Execution Lifecycle and Skill Artifacts HTTP Contract
description: Canonical API Gateway contract for calibration drafts, runs, activation previews, manual
  verifications, and declarative skill artifacts.
when_to_use: Use when discussing the proposed draft-based calibration lifecycle or declarative skill artifacts;
  neither exists in the current llm-service OpenAPI yet.
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 2
tags:
- calibration
- contracts
- gateway
- http
- llm-service
- openapi
- skills
- versioning
---

## Rule

Two different things are documented here: the **real, implemented** calibration flow, and a **proposed** draft-based flow plus skill artifacts that do not exist in the executable contract yet. Do not build against the proposed routes below â they are not in `llm-service.openapi.yaml`.

## Implemented today (`llm-service.openapi.yaml`)

All under `/api/llm/**`, M2M JWT (`aud=llm-service`), `Idempotency-Key` where the OpenAPI declares it, `traceparent`, `X-Request-Id`.

| Operation | Route |
|---|---|
| Create a calibration run directly | `POST /courses/{courseId}/calibrations` â body `{ rubricVersionId, goldenSetVersionId }`, no draft step |
| Get a run | `GET /courses/{courseId}/calibrations/{runId}` |
| Preview activation | `POST /courses/{courseId}/calibrations/{runId}/activate-preview` â `200 ActivationPreview` or `409` |
| Activate | `POST /courses/{courseId}/calibrations/{runId}/activate` â body `{ previewToken, challengeIds }` â `200 ActiveCalibration` or `409` |

Only `PASSED` runs can be activated. A `PASSED` run does not become `ACTIVE` automatically â activation is a separate, explicit step.

## PROPOSED, NOT IMPLEMENTED â draft-based flow (D-161, D-164)

A richer flow with private, editable drafts before running was proposed but is not built:

- `POST /api/llm/courses/{courseId}/calibration-drafts/{draftId}/runs` â would materialize an immutable snapshot from a private draft.
- `POST /api/llm/courses/{courseId}/calibration-runs/{runId}/activation-previews` â proposed alternate shape of the real `activate-preview` above, with `previewId`, `expiresAt`, migratable vs. locked challenges.
- `POST /api/llm/courses/{courseId}/calibration-activations` â proposed alternate shape of the real `activate` above.
- `POST /api/llm/admin/calibration-activations/{activationId}/verifications` â periodic/manual re-verification, `reason` mandatory, never an automated model fallback on failure.

None of these should be assumed available; the real flow above is what exists today.

## PROPOSED, NOT IMPLEMENTED â Skill Artifacts (D-01, D-16, D-124, D-128 to D-131)

There is no `/skills` route in the current OpenAPI. Proposed rules, if built:

- Markdown (`.md`) text only; no code execution, plugin runtimes or external API calls (D-16).
- Rejects embedded `<script>`/`<iframe>`/`<form>`, external image tags, and hyperlinks outside platform domains; code blocks parsed strictly as literal text (D-129 to D-131).
- Uploading an edit creates a new immutable `SkillVersion` with a `contentHash` (SHA-256); calibrations and student evaluations freeze the exact skill version snapshots used and retain them indefinitely; favorites reference a specific `skillVersionId` and do not auto-migrate (D-01, D-04, D-06, D-14).

## Open point

Both proposed sections require approval before implementation; see `docs/contracts/02-contratos-skills-y-calibracion.md` in the llm-service repo for the full, current status of each.