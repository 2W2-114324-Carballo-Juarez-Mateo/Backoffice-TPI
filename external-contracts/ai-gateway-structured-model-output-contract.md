---
slug: ai-gateway-structured-model-output-contract
title: AI Gateway Strict Structured Model Output Contract
description: Internal SPI contract enforcing strict structured JSON response, profileHash integrity, scoring
  unit validation, and typed failure codes.
when_to_use: Use when configuring LLM evaluator prompts, validating structured JSON responses in AI Gateway,
  or checking unitId score integrity against EffectiveEvaluationProfile.
stack: java
type: contract
owning_team: EvaluaciÃ³n LLM
version: 1
tags:
- ai-gateway
- contracts
- evaluation-profile
- json-schema
- llm-service
- scoring
- structured-output
---

## Rule

All AI models integrated via the AI Gateway for evaluation in `llm-service` must enforce strict structured JSON output compliant with this canonical contract. The model only scores atomic evaluation units (`unitId`); the server strictly calculates weighted dimensions, aggregate scores, and PAR-14 metrics.

## Canonical Model Output Schema (D-166)

The model must return strict JSON matching this structure:
```json
{
  "schemaVersion": "1.0",
  "profileHash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
  "scores": [
    { "unitId": "AUTONOMY", "score": 74 },
    { "unitId": "AUTONOMY/PLANIFICATION", "score": 70 },
    { "unitId": "CLARITY", "score": 85 },
    { "unitId": "PROGRESSION", "score": 88 },
    { "unitId": "COMPLIANCE", "score": 100 },
    { "unitId": "EFFICIENCY", "score": 75 }
  ]
}
```

- `unitId`: Identifies each active rubric dimension or subcriterion materialized by the `EffectiveEvaluationProfile` (D-160).
- `profileHash`: SHA-256 hex digest of the effective profile snapshot passed in the invocation.
- `score`: Integer strictly bounded between 0 and 100.

## Strict Acceptance Pipeline (D-166)

Before accepting any model response for calibration or student evaluation, the AI Gateway pipeline executes these 5 checks:
1. **Transport & Declared Mode**: Validates provider response headers, payload size boundaries, and declared JSON schema mode.
2. **Strict Parser**: Parses JSON without repair heuristics. Unrecognized fields, missing fields, or invalid types fail immediately.
3. **Profile & Unit Matching**:
   - `profileHash` must exactly match the invocation snapshot.
   - The set of `unitId` entries in `scores` must be a 1-to-1 exact match with the active units of the profile: no missing units, no duplicate units, no unknown units.
4. **Range Enforcement**: Every score must be an integer between 0 and 100. Floating point values are rejected.
5. **Deterministic Calculation**: The server applies configured weights and calculates dimension aggregates. Any model attempt to dictate overall grades or pass/fail decisions is discarded.

## Standard Error Codes
On rejection, the evaluator records one of these typed failure codes:
- `MODEL_RESPONSE_MALFORMED`: Invalid JSON syntax.
- `MODEL_RESPONSE_SCHEMA_MISMATCH`: Schema validation error or missing required fields.
- `MODEL_RESPONSE_PROFILE_MISMATCH`: `profileHash` does not match the active evaluation profile.
- `MODEL_RESPONSE_UNIT_UNKNOWN`: Model included a `unitId` not present in the profile.
- `MODEL_RESPONSE_SCORE_MISSING`: An active dimension or subcriterion was omitted.
- `MODEL_RESPONSE_SCORE_DUPLICATED`: Multiple scores provided for the same `unitId`.
- `MODEL_RESPONSE_SCORE_OUT_OF_RANGE`: Score is negative, > 100, or non-integer.

## Failure Policies
- Calibration runs fail terminally with the specific code; no automatic retry loop is permitted (D-156).
- Deferred student evaluations follow backoff retry policies up to the maximum limit (D-162); partial scores are never persisted or published.
