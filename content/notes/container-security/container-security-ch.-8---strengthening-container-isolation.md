+++
date = "2024-09-29"
draft = false
tags = ["container-security"]
title = "8 - Strengthening Container Isolation"
+++
We examine here **sandboxing** mechanisms, which isolate applications and restrict what they can do.

## seccomp

[seccomp](https://en.wikipedia.org/wiki/Seccomp), or "secure computing mode", was introduced in Linux in 2005 and limited what syscalls could be made. Since 2012, it uses BPF (Berkeley Packet Filter) as **seccomp-bpf** to create profiles that determine what to do when a given syscall is made. The filter looks at the syscall opcode and parameters, and makes a decision on whether to allow it, or how to trace it, and so on.

The default Docker seccomp profile blocks around 40 of the 300+ Linux syscalls, and is sufficient for most applications without limiting functionality at all. For example, most applications don't need to mess with Linux kernel modules or change the host time, so we block calls like *clock_adjtime* and *create_module*.

**Note**: since Kubernetes v1.19 (released 2021), [seccomp profiles](https://kubernetes.io/docs/tutorials/security/seccomp/) are available in Kubernetes, in addition to things like [AppArmor](https://kubernetes.io/docs/tutorials/security/apparmor/) and [Pod Security Standards](https://kubernetes.io/docs/tutorials/security/cluster-level-pss/).

## AppArmor

[AppArmor](https://apparmor.net/) is a [Linux Security Module (LSM)](https://en.wikipedia.org/wiki/Linux_Security_Modules) that enables **mandatory access control**, where an admin enforces policies on users that cannot be modified by users or propagated to other users. This is in contrast to **discretionary access controls**, like Linux file permissions, where a user that owns a file can then do all kinds of things to that file.

AppArmor has a few tools and filesystem locations of note:

- *aa-status*: broad overview of AppArmor
- */etc/apparmor.d*/: location of AppArmor application profiles (possibly user ones in /etc/apparmor?)
- */sys/module/apparmor/*: AppArmor functionality; parameters/ controls how AppArmor runs.
- Always use *auditctl* and *journalctl* for systemd distributions.

## SELinux

[**Security-Enhanced Linux**](https://en.wikipedia.org/wiki/Security-Enhanced_Linux)[ (SELinux)](https://en.wikipedia.org/wiki/Security-Enhanced_Linux) is another Linux Security Module (LSM) that works orthogonally to DACs like file permissions and ownership. It uses a system of labels on everything - files, sockets, users - and checks actions per user against the type of object to see if it's permitted. You can view SELinux labels via *ls -lZ* - without a label, no policy can be enforced on the file. Some useful links:

- [Visual SELinux Guide (Dan Walsh)](https://opensource.com/business/13/11/selinux-policy-guide)
- [Intro to SELinux on Centos (Digital Ocean)](https://www.digitalocean.com/community/tutorials/an-introduction-to-selinux-on-centos-7-part-1-basic-concepts)