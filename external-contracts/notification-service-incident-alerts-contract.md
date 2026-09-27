---
slug: notification-service-incident-alerts-contract
title: Notification Service Request and Critical Incident Alert Contract
description: Contract for requesting critical notifications and publishing LLM security incidents, calibration
  suspensions, and evaluator outages to notification-service.
when_to_use: 'Use when discussing what llm-service proposes to send notification-service: calibration
  suspensions, evaluator outages, or security incident alerts. No topic or eventType is assigned yet.'
stack: shared
type: contract
owning_team: EvaluaciÃ³n LLM
version: 2
tags:
- alerts
- contracts
- grupo-notificaciones
- incidents
- kafka
- llm-service
- notification-service
---

## Rule

`llm-service` has **no topic or eventType assigned yet** for requesting notifications or reporting incidents to `notification-service`. Everything below is a proposal (D-21, D-22, D-165), not a contract in force. Groups cannot create topics on this platform; the notifications team assigns the final name. When published, it will use the platform envelope `{eventId, eventType, timestamp, producer, payload}`, no `eventVersion`, correlation (`traceparent`, `X-Request-Id`) exclusively in Kafka headers.

## Proposed: notification request

Candidate `eventType`: `NOTIFICATION_REQUESTED` (not final). `llm-service` would publish a request via outbox; `notification-service` would own recipient resolution, delivery and read/unread state â `llm-service` never delivers directly or tracks read status.

Proposed `payload`:
```json
{
  "notificationRequestId": "uuid",
  "incidentId": "uuid | null",
  "type": "CALIBRATION_SUSPENDED",
  "severity": "HIGH",
  "recipientScope": { "kind": "ADMIN_ROLE | COURSE_AUTHORIZED_TEACHERS | USER", "courseId": "uuid | null", "userId": "uuid | null" },
  "resource": { "courseId": "uuid | null", "challengeId": "uuid | null" },
  "reasonCode": "string"
}
```

Technical deduplication would use the envelope's `eventId`. To avoid repeated business alerts, `llm-service` proposes outbox uniqueness on `(incidentId, type, recipientScope, phase)`, `phase` one of `OPENED`, `RETRY_EXHAUSTED`, `RECOVERED`.

## Proposed incident/outage facts (no topic or eventType assigned)

None of these are implemented. Documented as **facts**, not topic names, so no name is assumed before Notifications assigns one:

| Fact | Minimum payload | Recipient scope |
|---|---|---|
| Active calibration invalidated (skill incident or verification failure) | `activationId`, `scope`, `courseId`, `challengeId`, `reasonCode` | Course teachers, Admins |
| Evaluator model total outage opens | `incidentId`, `deploymentId`, `reasonCode` | Admins, affected teachers |
| Evaluator model total outage recovers | `incidentId`, `deploymentId` | Admins, affected teachers |
| Deferred attempt score exceeded max retries | `evaluationId`, `courseId`, `challengeId`, `attemptId`, `attemptCount` | Operations, Admins |

## Boundaries (agreed, independent of the topic names above)

- `llm-service` owns local security incident records, audit logs, calibration suspension flags and its own outbox rows.
- `notification-service` owns recipient resolution, delivery through push/email/web, and read/unread state.
- Payloads never include skill Markdown, system prompts, student transcripts or secrets.

## Open point

Topic name, final `eventType`s, recipient scopes, severities and DLQ treatment must be approved by `notification-service` before any of this is implemented.