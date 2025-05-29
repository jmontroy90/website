+++
date = "2024-10-09"
draft = false
tags = ["container-security"]
title = "5 - Virtual Machines"
+++

Virtual machines and Docker containers have different security boundaries. Fundamentally, virtual machines run an ENTIRE operating system including the kernel, whereas Docker containers share the host machine's kernel. We can do a lot to harden up a container, but this distinction will remain.

Normally when a server boots, it discovers via [BIOS](https://en.wikipedia.org/wiki/BIOS) what resources are available (memory, CPU, network interfaces), boots the kernel, and eventually enters user space. In the VM world, we insert a [**Virtual Machine Monitor (VMM)**](https://en.wikipedia.org/wiki/Hypervisor) before that process, meaning that the VMM discovers physical resources, and then allocates some slice of resources to each guest VM it's hosting. The VMM is responsible for creating and enforcing resource boundaries between its guest VMs. Meanwhile, the guest VM gets to be "normal", discovering available resources same as always via BIOS, with no awareness that the VMM has fixed those resources already. 

There are two types of VMMs, Type 1 and Type 2:

* *Type 1*: Hypervisors. The bootloader runs a dedicated VMM kernel instead of an OS kernel. This would be things like Hyper-V or ESX.
* *Type 2*: Hosted. These are your VirtualBox, Parallels, even probably [multipass](https://github.com/canonical/multipass/) counts

There's also a sorta in-between with [**Kernel-Based Virtual Machines (KVMs)**](https://en.wikipedia.org/wiki/Kernel-based_Virtual_Machine), which uses the hypervisor built into the Linux kernel to manage virtual machines.

## Kernel Code / System Calls in VMs

Remember that a guest OS issues system calls and runs kernel code as if it's running at "Ring 0", e.g. the most privileged tier of x86 instruction execution. In actuality, a guest OS is running at Tier 1, and in a Type 1 VMM, the hypervisor is running at Tier 0:

So when the guest OS issues instructions that require tier 0 privileges, what happens? This situation occurs when an x86-based guest OS issues a **sensitive, non-privileged** command (also known as a "[non-virtualizable instruction](https://stackoverflow.com/questions/32794361/what-are-non-virtualizable-instructions-in-x86-architecture)") - sensitive meaning it relates to actual system resources (of which only the VMM has true knowledge), and privileged meaning must be executed in Ring 0. The VMM has several options here:

1. **Binary Translation**: "All the non-privileged, sensitive instructions in the guest OS are spotted and rewritten by the VMM in real time."
2. **Paravirtualization**: "Instead of modifying the guest OS on the fly, the guest OS is rewritten to avoid the non-virtualizable set of instructions, effectively making system calls to the hypervisor."
3. **Hardware Virtualization**: "Allows hypervisors to run in a new, extra privileged level known as VMX root mode, which is essentially Ring –1. This allows the VM guest OS kernels to run at Ring 0 (or VMX non-root mode), as they would if they were the host OS."

## Comparison

* **Complexity**: VMMs are much simpler pieces of software.
  * Sure, there could be exploits in a hypervisor that allows an attacker to access memory allocated to other guest VMs, but the attack surface is much, much smaller. 
  * For example, the Linux kernel is 20 million lines of code and alway changing; the Xen hypervisor is only 50,000 lines of code. The Linux kernel just has a lot more complexity, and thus a much larger attack surface!
* **Start-Up**: VMs take much longer to boot on average, although things like Amazon's Firecracker are nipping at container's heels. Each VM is running an entire kernel, which is hefty.
* **Efficiency**: Containers have great developer lifecycle patterns, and allow for fast and productive iteration. VMs could catch up, but the ecosystem isn't there.
* **Security**: Because VMMs are simpler, VMs have better security boundaries out of the box. But there's a lot we can do with containers to get closer to VM-like security boundaries - to be covered in future chapters!