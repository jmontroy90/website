+++
date = "2024-09-27"
draft = false
tags = ["container-security"]
title = "1 - Container Security Threats"
+++

(recently finished reading [Container Security](https://www.oreilly.com/library/view/container-security/9781492056690/) by Liz Rice; excellent, well-structured read, highly recommended. I'll be posting my notes on it for the next few days.)

Docker is not the first isolation mechanism, but its CLI tooling really popularized the technology around 2013. Orchestrators like Kubernetes followed shortly thereafter.

Containers are great. They provide isolation, reproducibility, and enable orchestration on a single host. But they also have an attack surface like any environment.

We mitigate risk by performing threat analysis - how might attackers steal data, for example? Compromising some day-zero exploit in a web service? SQL injection? These are threats, with the risk being "stolen data".

### Container Threats

There are many kinds of container threats - internal vs. external, accidental vs. intentional, and sometimes just rogue processes with excess permissions. Each of these threats might have different access - credentials, permissions, network access, etc. And in a virtualized world, these threats can occur via many different vectors.

### Security Boundaries

Be aware of your many security boundaries: containers vs. virtualization vs. data centers vs. multitenancy. All different. The more boundaries an attacker must go through, the better.

Different boundaries have different strengths - VMs are considered quite isolated from one another, with no access to the hypervisor running them. Containers, however, are less isolated. Shared machines are obviously the least isolated of all, but even with total isolation, we can still run into "noisy neighbor" issues. These might be exacerbated by things like AWS Fargate, where you get a container poofed into existence with no knowledge of where it is or what else is running on the bare metal.

### Security Privileges

- **Least Privilege**: no more privileges than you need. If you don't write, grant read-only.
- **Defense in Depth**: if one security layer fails, another will hopefully succeed.
- **Reducing the Attack Surface**: the less code, access points, dependencies, even users — the better.
- **Limiting Blast Radius**: this goes for deploys too. If a component fails, it shouldn't cascade through a broader system.
- **Segregation of Duties**: each component should try to do one thing well, and only that one thing.

Containers help with all of these (e.g. least privilege - just grant each container what it needs), except for arguably "reducing the attack surface" because splitting into microservices might increase attack surface area. But by the end of the book, we'll have understood these boundaries, threat models, and how we can properly use containers to follow these privileges.