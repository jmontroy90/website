+++
date = "2024-11-06"
draft = false
tags = ["container-security"]
title = "12 - Passing Secrets to Containers"
+++

We start with a couple of desirable secrets properties for applications:

- **Encrypted**: both in transit and at rest. Decrypted by another secret, preferably only in memory.
- **Revocable**: if something / someone is compromised, we need to be able to revoke the secret.
- **Rotatable**: forcing humans to change passwords frequently is bad, but machines can handle it.
- **Restricted**: minimal access, even for developers of the app in question.
- **Independent**: the secret's lifecycle should be independent of the apps that use it.

What are our options for getting a secret into a container? Here we evaluate each in turn:

- **Baked Into Image**: no good. This couples secrets to the container build, and gives access to anyone who can view the source code.
- **Environmental Variables**: no good, despite 12-Factor App recommendations. Logs or crash dumps can reveal environmental variables, and things like `docker inspect` will show both build and runtime environmental variables.
  - If your library expects env vars, try to conceal sensitive variables in your logs, and retrieve the secrets from some secrets solution like Vault.
- **File Mount From Host**: this is preferred. The secret can be rotated and reloaded by the container without a restart, because the host (or some secrets solution) can modify the host file without touching the container. You can also mount a temporary directory instead of a real one, such that the secret is always in memory.

[Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) meet our criteria:

- They're their own Resource, independent of apps.
- They can be encrypted at rest (opt-in), and Kubernetes components (e.g. kubelets set up with proper PKI) will encrypt them in transit.
- RBAC allows for user-level access with good granularity.
- Secrets can be mounted as temporary files in memory.

Most places (including all places I've worked) end up using things like AWS KMS, Vault, etc. They allow multi-cluster sharing, rotation, independent editing, etc.

Lastly, as always - if you have root on a host machine, you can see everything, including secrets. Mounts are visible via _mount -t tmpfs_ and env vars are under _/proc/<container-process-PID>/environ_.