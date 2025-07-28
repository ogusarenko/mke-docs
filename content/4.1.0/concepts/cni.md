---
title: Container Network Interfaces
weight: 4
---

MKE 4k supports Container Network Interface (CNI) plugins to enable the
networking functionalities needed for container communication and management
within a cluster. These plugins are responsible for setting up, modifying, and
tearing down network interfaces when containers are created or removed. They
provide MKE 4k with a wide range of networking functionalities, including IP
address management, routing, NAT, and network isolation.

CNI plugins that MKE 4k currently supports include:

- [Calico OSS](../../configuration/container-network-interface/configure-cni-providers#calico-oss)

{{< callout type="important" >}}

MKE 4k supports upgrades from MKE 3 clusters that are using Calico OSS or an
unmanaged CNI. For more information, refer to [Upgrade from MKE 3.7 or
3.8](../../upgrade-from-mke-3x).

{{< /callout >}}
