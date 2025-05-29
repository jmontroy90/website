+++
date = "2024-10-08"
draft = false
tags = ["container-security"]
title = "4 - Container Isolation"
+++

Container isolation is achieved through a combination of Linux kernel features.

## Namespaces

Docker containers are primarily built from two Linux primitives - cgroups (covered last time) and **namespaces**. Cgroups cover resource management per process - namespaces control what is visible to a process. Since a Docker container is nothing more than a Linux process, we can use namespaces of various types to control visibility of these resources:

- Unix Timesharing System (UTS) - basically hostnames and domain names.
- Process IDs
- Mount points
- Network
- User and group IDs
- Inter-process communications (IPC)
- Control groups (cgroups)

A process is always in one namespace of each type. We can see this immediately with the *lsns* command:

```
> lsns
        NS TYPE   NPROCS   PID USER             COMMAND
4026531834 time      103     1 root             /sbin/init
4026531835 cgroup    103     1 root             /sbin/init
4026531836 pid       103     1 root             /sbin/init
4026531837 user      103     1 root             /sbin/init
4026531838 uts        98     1 root             /sbin/init
4026531839 ipc       103     1 root             /sbin/init
4026531840 net       103     1 root             /sbin/init
4026531841 mnt        94     1 root             /sbin/init
```

Via bash, new namespaces can be created with the *unshare* command, which creates a subprocess running in a new namespace of the specified type:

```
> sudo unshare --pid --fork sh
> sudo unshare --mount sh
> sudo unshare --net bash
```

So the first one gives a process its own view of PIDs, starting from zero; the second its own device mounts; and the third allows for network namespaces which can enable routing between a container's network namespace and the host's! 

User namespaces are useful for running rootless containers in Docker - [as of recently](https://docs.docker.com/engine/security/rootless/), rootless mode can even allow the Docker daemon (plus containers) to all run as non-root, while still allowing users in containers to be root.

## chroot

The last bit of isolation we need, in addition to granular resource management (cgroups) and resource isolation (namespaces), is *chroot*, e.g. "Change root". This allows a container to set its root filesystem location */* to somewhere else, effectively making everything above that location invisible. 

So I can use the following commands on my Ubuntu 24.04 LTS VM to download an *aarch64* Alpine distribution, and chroot into it as a new root filesystem, using its binaries - it essentially already feels like an Alpine container at that point!

```
mkdir newroot
curl -o newroot/alpine.tar.gz  https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/aarch64/alpine-minirootfs-3.20.2-aarch64.tar.gz
tar -xzvf newroot/alpine.tar.gz -C newroot
rm -f newroot/alpine.tar.gz
chroot newroot sh
```