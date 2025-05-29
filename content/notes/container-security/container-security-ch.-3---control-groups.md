+++
date = "2024-09-29"
draft = false
tags = ["container-security"]
title = "3 - Control Groups"
+++

**Control groups**, a.k.a. **cgroups**, controls the resources that a process or group of processes can use. This includes things like memory, CPU, prioritization, and even number of PIDs. They were introduced into Linux in 2008, and have two major versions. *v2* was released around 2015, and is approaching stable adoption.

You can see your cgroups in various ways:

* **lscgroup** from the cgroup-tools package - a nice formatted overview
* **ls -la /sys/fs/cgroup** - the raw files, managed by your system.
* **cat /proc/$$/cgroup** - a given process's cgroups, in this example your current PID's ($$)
* **systemctl status** - lists lots of nicely-formatted info on processes, including your current user session. The CGroup is prominently listed.

If you want to see all resources that have cgroup controllers: 

```
> cat /sys/fs/cgroup/cgroup.controllers
cpuset cpu io memory hugetlb pids rdma misc
```

Managing cgroups can be done manually via creating directories (e.g. cgroups!) under */sys/fs/cgroup*, and those directories will be populated with the requisite resource files for management. In practice, it's probably just best to find some tooling to do this for you, like what comes with *cgroup-tools*.

Docker and Kubernetes (or rather, Kubernetes via Docker) just use cgroups to manage resource limits in specified in your Pod config. You can see Docker containers under the *system.slice/* cgroup, as well as the Docker daemon and Docker socket cgroups:

```
> ls /sys/fs/cgroup/system.slice/ | grep docker
docker-1b11d97404bfc1e89c28db8cfa9bc9d33535cd0378ced515ecae5b65d6632a77.scope/
docker.service/
docker.socket/
```

So for example, every process you start in the Docker container listed above will have resources managed by that first *docker-1b11...* scope, such that as you create processes in the container, you can watch the *pids.current* resource tick up:


```
# on host
> cat /sys/fs/cgroup/system.slice/docker-1b11<...>.scope/pids.current
2

# in container
> sleep 100 &

# on host 
> cat /sys/fs/cgroup/system.slice/docker-1b11<...>.scope/pids.current
3
```

You can also watch *pids.current* tick up inside the Docker container, where there's just one top-level cgroup (containers are light!).

Meanwhile, processes started within your sessions will fall under the user.slice/ scope as various sessions. *systemctl status* shows all this nicely.

### CGroups V2

Ubuntu 24.04 LTS uses **cgroups v2**. The main difference between cgroups v1 and v2 is how they approach hierarchy - v1 approaches it from a resources perspective, and v2 approaches it from a processes perspective. From this [Medium article](https://medium.com/some-tldrs/tldr-understanding-the-new-control-groups-api-by-rami-rosen-980df476f633) (via [this KubeCon 2022 talk](https://www.youtube.com/watch?v=WxZK-UXKvXk)):

The details of this aren't particularly important to this compared to the big picture - v1 enables "flexibility", but in practice is kinda a mess:

- Most people want to manage resources on a process level. Managing cgroups via resources instead means a lot of coordinated resource changes across resource controllers.
- In v1, processes and even threads could belong to different resource cgroups, which can get really really complicated. In v2, that's not allowed.
- Subgrouping, and parameter propagation to those subgroups, got really messy in v1 because of the flexibility (some controllers propagated to subgroups, some didn't!). The single, unified hierarchy of v2, working at a process level, makes it much easier and more clear to see what's going on.

V2 also introduces **Pressure Stall Information**, which basically gets us intel on resource usage over time, rather than just point-in-time statistics that mask how utilization behaves (bursty? steady increase?).

Lastly, V2 enabled resource limits for rootless containers, e.g. containers don't have to run as root any more to use cgroups! Really great!