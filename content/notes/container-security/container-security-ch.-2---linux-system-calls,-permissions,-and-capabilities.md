+++
date = "2024-09-28"
draft = false
tags = ["container-security"]
title = "2 - Linux System Calls, Permissions, and Capabilities"
+++

### System Calls

All applications run in user space. Applications can make system calls, which involve interacting with the kernel. Since all containers are applications on one Linux host e.g. one kernel, managing system calls from containers is important. Some example system calls are things like *read*, *write*, *chown*, *clone*, *execve*. Rarely do we make any system calls directly - in Golang, you might use the *glibc* library in the *syscall* package, but that's at most.

### File Permissions

Everything on Linux is a file. Config, logs, sockets, printers, devices.

If you see an *s* instead of *x* for the executable permission on a file, it has **setuid** set. setuid tells the file to execute as the owning user instead of as the user who actually executes the file. So if user "john" owns a binary which has setuid (chmod +s ./mybinary), then no matter who executes it, it'll run as user "john".

The canonical example is **ping** - anyone should be able to execute ping, which opens a raw network socket. But preferably almost no one should be able to create a raw network socket outside of ping! So ping is owned by root and has setuid set, which means anyone executing ping will run it as root.

Note: recent Ubuntu distributions (e.g. 24.04 LTS) don't use setuid and instead rely purely on capabilities - see section below for a description, and here's what you get with getcap:

```
getcap $(which ping)
/usr/bin/ping cap_net_raw=ep
```

setuid is old and dangerous -- it's a common pathway to privilege escalation, such that vulnerability scanners will usually flag it. See this small example on how to get root access via Docker volumes.

Two more settings:

- **setgid** is like setuid but for running as the owning group.
- The **sticky bit** set on a directory prevents files in that directory from being deleted by anyone except root or the owner.

### Capabilities

Nowadays, what a binary / thread can do is managed through capabilities. Capabilities can be assigned to threads, and to files via setcap, and you can see a process's capabilities with *getpcap*. Some big capabilities:

- **CAP_NET_BIND_SERVICE**: bind to ports lower than 1024
- **CAP_SYS_BOOT**: arbitrary executable can't reboot
- **CAP_NET_RAW**: create network sockets

### Privilege Escalation

That's any time someone can get privileges they shouldn't have. The above Docker vulnerability is an example. setuid is a problem here, and capability management is a problem.

Remember: by default, containers run as root. That means applications in the container run as root by default, and if someone can escape the container, they've got system root access. More to come.