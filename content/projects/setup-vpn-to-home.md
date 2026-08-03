---
title: "Setup VPN to Home"
date: 2026-07-20
status: "planned"
priority: "medium"
started: 2026-07-20
target:
progress: 10
tags: [networking, homelab]
lastLog:
  date: "07/29"
  text: "Waiting on router firmware update before WireGuard port-forward will stick."
---

## Scope

**In scope:**
- WireGuard tunnel from laptop / phone back to home network
- Access to home server and local devices while away

**Out of scope:**
- Full site-to-site VPN between multiple locations

## Deliverables

- [ ] Router firmware updated
- [ ] WireGuard server configured on home box
- [ ] Port forward + dynamic DNS set up
- [ ] Client config tested from outside network

## Resources & dependencies

- Router admin access
- Blocked on: router firmware update

## Definition of done

- Can connect from an external network and reach home devices reliably

## Log

- **2026-07-29** — Waiting on router firmware update before WireGuard port-forward will stick.
- **2026-07-20** — Project started, picked WireGuard over OpenVPN.
