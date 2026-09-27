---
slug: backoffice-par-client-with-cache
title: Backoffice PAR client with cache
description: Fetches Backoffice PAR parameters via REST with in-memory cache and event-driven invalidation
  instead of hardcoding them.
when_to_use: Use when reading Backoffice PAR parameters, configuration values owned by another team, or
  whenever tempted to hardcode a PAR default; also when handling GLOBAL_CONFIGURATION_CHANGED events.
stack: java
type: convention
owning_team: Roadmap y Progreso
version: 1
---

## Rule

Fetch Backoffice PAR parameters over REST through the Gateway on first use, cache them in memory, and refresh only when the `GLOBAL_CONFIGURATION_CHANGED` event arrives. Never hardcode PAR values or keep them in environment files as the source of truth.