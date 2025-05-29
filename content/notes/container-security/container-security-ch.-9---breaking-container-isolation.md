+++
date = "2024-10-13"
draft = false
tags = ["container-security"]
title = "9 - Breaking Container Isolation"
+++


Here we cover ways that container isolation can be broken, mostly accidentally.

## Running as root

The Docker daemon requires root access - already a concern (also: anyone in the *docker* group essentially has root access). But unless otherwise specified, all containers will run as *root* inside the container. Processes started as root in the container will appear as root on the host. So if you're able to break out of a container as root, you can do whatever you want. The *USER* command in a Dockerfile will set a non-root user, as will the *--user* flag on the *docker run* subcommand.

A lot of software was built to run as root, well before Docker was around. Take nginx as an example - it requires the *CAP_NET_BIND_SERVICE* capability to bind to ports under 1024. Fortunately, nginx - and many other pieces of software - can be rewritten such that root access is not needed. [docker-nginx-unprivileged](https://github.com/nginxinc/docker-nginx-unprivileged) is exactly that, and [Bitnami](https://oreil.ly/W4nV2) has rewritten lots of other software to remove the root requirement.

Some people run as root to install packages in the container. Don't do that.

## Rootless Containers

We can use user namespaces to map root in a container to some non-root user outside of it. Also, runtimes like Podman don't require a daemon with root, and so supports rootless containers more easily. 

Capabilities are namespaced as well when you create a user namespace. So even though you have "root" in a container, you still might not be able to do certain things. Also, file permissions and groups need to be mapped from the host into the user namespace as well. Rootless containers seem to be the future, but they're relatively new.

## The --privileged flag

As mentioned, even when a container runs as root, it lacks a lot of important capabilities (OCI keeps a [default set](https://oreil.ly/ryVjj)). The *--privileged* flag grants the container root user a huge number of capabilities, including *CAP_SYS_ADMIN*, which can manipulate namespaces and mount filesystems, among other things.

This flag was introduced for "Docker in Docker" problems, and so if this isn't your use case, ask yourself why you need -*privileged*.

Tools like [Tracee](https://oreil.ly/1dQof) can show you syscalls; a good approach (principle of least privilege!) would be to drop all capabilities, and then re-introduce the needed ones based on your tracee audit:

```
$ ./tracee.py -c -e cap_capable
$ docker run --cap-drop=all --cap-add=<cap1> --cap-add=<cap2> <image> ... 
```

## Other Problems

1. **Mounting Host Filesystem**: mounting things like */etc*, */var/log*, */bin* all by default give that container root access to those folders on the host.
2. **Mounting the Docker Socket**: a common CI/CD task, but any container with access to the Docker socket essentially has root on the host.
3. **Sharing Namespaces with the Host**: running *docker run --pid=host ...* will expose the process namespace of the host to that container. Since all container processes are visible on the host, that also gives you visibility into all other container processes, including the ability to send kill signals to them!

Note that sidecars (e.g. for mTLS, observability, security audits) can share namespaces and that's totally legit.