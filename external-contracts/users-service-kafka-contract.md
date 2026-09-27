---
slug: users-service-kafka-contract
title: users-service Kafka contract
description: Topics, event types and payloads users-service publishes and consumes over Kafka.
when_to_use: Use when consuming users-service events (STUDENT-REGISTERED, ACCOUNT-DEACTIVATED, notification
  emails) or publishing COURSE-VALIDATION-RESOLVED to it.
stack: shared
type: contract
owning_team: Identidad y Usuarios
version: 1
tags:
- contracts
- course-events
- events
- kafka
- notification-events
- user-events
- users-service
---

## Rule

Key every users-service event by `userId` â the key travels outside the JSON envelope â and treat `COURSE-VALIDATION-RESOLVED` as a draft until the Courses team confirms its message key. Never change a payload without telling the other side.

## Published

**`user-events`** (key `userId`, version 1, producer `tema-01-users`):

- `STUDENT-REGISTERED` â the student verified their email and entered course validation. Payload: `userId`, `studentNumber`, `invitationCode`.
- `ACCOUNT-DEACTIVATED` â logical deactivation by ADMIN or GESTOR (the row is never deleted; the session is closed). Payload: `userId`, `role`, `deactivatedBy`, `deactivatedAt` (ISO-8601 UTC). Known consumer: none yet.

**`notification-events`** (key `userId`, version 1) â rendered emails for the notifications service. Payload: `to`, `subject`, `html`. Event types: `TWO-FACTOR-EMAIL-PREPARED`, `ACCOUNT-ACTIVATION-EMAIL-PREPARED`, `PASSWORD-RESET-EMAIL-PREPARED`, `REQUEST-PENDING-EMAIL-PREPARED`, `ENABLING-RESOLVED-EMAIL-PREPARED`, `BREAKGLASS-ALERT-EMAIL-PREPARED`, `WHITELIST-SUBMISSION-EMAIL-PREPARED`, `WHITELIST-DECISION-EMAIL-PREPARED`.

## Consumed

**`course-events`** from `tema-02-cursos`: `COURSE-VALIDATION-RESOLVED` version 1, payload `userId`, `result`, `courseId`. The message key is still pending definition by the Courses team: this service does not infer or redefine it.

## Envelope and delivery

Every value carries `eventId`, `eventType`, `eventVersion`, `timestamp`, `producer`, `payload`. Unknown event types and versions are ignored; invalid envelopes or payloads are rejected before any business state changes. Events are written to an outbox in the same SQL transaction as the data change and dispatched by a poller that retries failed publications: consumers must tolerate repeated events.