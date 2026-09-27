---
slug: micro-on-the-tailscale-mesh
title: Micro on the Tailscale mesh
description: 'How a team''s micro joins the shared server over Tailscale: the sidecar pattern and the
  hard rules that keep it safe.'
when_to_use: Use when a micro must run on the shared server over Tailscale, or when a sidecar, MagicDNS,
  tailnet IP, TS_AUTHKEY, network_mode service, or a gateway 503 is involved.
stack: infra
type: convention
owning_team: Identidad y Usuarios
version: 1
tags:
- docker-compose
- eureka
- magicdns
- mesh
- network-mode
- sidecar
- tailscale
---

## Rule

A micro joins the Tailscale mesh only through its own sidecar sharing the network namespace; it never publishes ports, never joins a Docker network, and never sets dns/extra_hosts of its own. The mesh is an L3 network (WireGuard), not a Docker network: a container does not speak Tailscale, it inherits the namespace of a sidecar.

## Team compose

- Sidecar: pinned tailscale/tailscale, TS_USERSPACE=false, /dev/net/tun, cap_add NET_ADMIN/NET_RAW, a state volume, and a healthcheck on BackendState Running.
- Micro: network_mode: "service:<sidecar>" + depends_on service_healthy (service_started starts it before tailscale0 exists). Reset networks, labels, expose and hostname on the micro: the daemon rejects expose/hostname together with network_mode: service: even when docker compose config accepts them.
- Sidecar sets dns: [100.100.100.100], so the micro's resolver answers Docker names locally and everything else via MagicDNS. Verify both a Docker name and a MagicDNS FQDN.
- Address every tailnet host by full FQDN (tpi-plataforma.tail767776.ts.net), never by IP.
- TS_AUTHKEY: reusable key tagged tag:microservicio, per environment, from Identity by a private channel, never in a repo.

## Registration

Register by tailnet IP, not by Docker IP: spring.cloud.inetutils.preferred-networks: ["100."] (no CIDR: each value is a regex/prefix), prefer-ip-address: true, and a unique instance-id per replica. Each replica needs its own sidecar; --scale reuses one netns and collides on the port.

## Safety

- Tagged ACLs are mandatory: without them "no ports:" protects nothing, any node reaches <micro-ip>:<PORT> and can spoof the gateway's X-* headers.
- Never forward or publish :8082 (users-service) or any platform port; JWKS goes through the gateway at :8080.
- A Vault Agent for the team shares this same sidecar namespace (network_mode: service:<sidecar>) and reaches Vault by MagicDNS.

## How to run

- team/up.sh mesh (loads docker-compose.mesh.yml; --vault adds the Vault Agent), then team/verify.sh mesh.
- Symptoms: 404 route-not-found = missing from the gateway allowlist; 503 with registration UP = no outbound route, ACL or stale IP; NXDOMAIN = missing dns: [100.100.100.100]; direct access to the micro from another node works = no ACL applied.