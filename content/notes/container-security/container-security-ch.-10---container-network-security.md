+++
date = "2024-10-14"
draft = false
tags = ["container-security"]
title = "10 - Container Network Security"
+++

## OSI Model (again)

Taking the average HTTP packet application, we have:

1. **Layer 1**: physical (usually) cabling and physical NIC ports.
2. **Layer 2**: e.g. Ethernet. All interfaces have an ARP address. MAC addresses are resolved to IP addresses via ARP requests, which broadcast to all connected devices to resolve an IP (similar to DNS resolving). ARP requests constantly buzz on a network to keep the ARP cache from going stale.
3. **Layer 3**: e.g. IP, ICMP. Internet addresses and hops between them. When traversing the broader internet via ISPs (BGP stuff!), data unwraps only to layer 3. No need to futz with higher stuff like ordering via TCP.
4. **Layer 4**: e.g. TCP, UDP. This is where port numbers get introduced for TCP / UDP. Stateful vs. stateless connections!
   1. TCP opens with a SYN / ACK exchange that synchronizes sequence numbers between two hosts, which can then be used to maintain packet order. For each message, the sequence number is incremented by the number of bytes found in the data portion of the packet. 
   2. UDP is stateless - packet order is not guaranteed, and packets can be dropped without warning.
5. **Layer 7**: there's always fuzziness in skipping up to layer 7, but, broadly speaking, this is stuff like HTTP, FTP, telnet, etc. - all plaintext protocols.

## Layer 3/4 Routing and Rules

Layer 3 concerns IP hops, but also (via Linux's *netfilter* framework) can implement NAT, firewalls, load balancing, ad security policies (e.g. dropping packets). We can also implement *netfilter* rules targeting Layer 4 primitives, like port or protocol.

In Kubernetes, kube-proxy uses *iptables* or IPVS (IP Virtual Server) to implement network rules around firewalls, overlay networks, etc. *iptables*, for example, has rule types for *nat* and *filter*. *iptables* gets really bulky for bigger clusters, so IPVS is used more commonly - Kubernetes clusters are constantly moving pods and containers around, so you have to rewrite rules a LOT. This can be a lot of per-node overhead with *iptables*!

If I recall correctly, there are TONS of network overlay solutions out there - might be worth watching a few videos on how things like Calico or Weave work, for a good refresher on Kubernetes networking.

## Network Policies

Networking policies can also be implemented using simple *netfilter*-based rules, where things like "drop packets from this labeled Pod".

Kubernetes doesn't implement this directly, despite providing the *NetworkPolicy* resource -- it's up to plugins like [Weave](https://github.com/weaveworks/weave) or [Calico](https://www.tigera.io/project-calico/) to do this.

There are some best practices around networking policies:

- **Default deny**: deny all ingress traffic, allow exceptions one by one.
- **Default deny egress**: don't let your pod talk to everyone.
- **Restrict ports**: open ports are how we get compromised.

Bear in mind - all of this is being implemented by networking plugins using layer 3 / 4 primitives! But we can implement more network functionality at layers 5-7!

## Service Mesh

Service meshes are useful in the sidecar model, e.g. Istio. We get things like mTLS, canary deployments (Flagger), application-level network policy ("I only want traffic from this particular service), logging / observability, and security (admission controls) with sidecars like Istio's.

All containers in a Pod share the same network namespace, including sidecars, so be careful of what your sidecars can do networking-wise vs. your application (does your application need the *CAP_NET_ADMIN* capability?).