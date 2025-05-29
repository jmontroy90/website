+++
date = "2024-11-07"
draft = false
tags = ["container-security"]
title = "13 - Container Runtime Protection"
+++

In order to best protect our service, we can build out a profile in different domains. In a lot of these domains we can observe events via eBPF (another Liz Rice book I might need to read).

- **Network Traffic**: what does ingress / egress traffic look like? What ports? Do we handle TLS?
- **Executable Profiles**: if we have immutable containers, we should basically have one executable running at any time (unless we shell out).
  - We can use [Tracee](https://github.com/aquasecurity/tracee) to view executed commands
- **File Access Profiles**: more eBPF.
- **User ID Profiles**: you don't need anything other than your single app user, right?
- **Capabilities**: use Tracee to see what Linux capabilities are used, then transform that into an AppArmor / *seccomp* profile for your container.

Things like Tracee are for detection - you need fuller systems for prevention. These systems should be able to actually take action when they detect a deviation. Lots of questions on how to run these auto-prevention systems:

- Should you delete a compromised container? Will that effect availability? Will you lose valuable telemetry / forensics?
- Is this version compromised? Is your orchestrator just spinning up vulnerable instances? Can you roll back? 

Lastly, drift prevention system allow you to ensure the integrity of your containers vs. their image. This is in the name of ensuring immutable containers. We create a fingerprint for files, and when an executable is run, that executable's fingerprint is compared to its original fingerprint. If they don't match, we shut it down.