---
aliases:
  - /latest/configuration/container-network-interface/
  - /docs/configuration/container-network-interface/
title: Container Network Interfaces
weight: 12
---

Being able to correctly configure and customize Container Network Interface
(CNI) plugins is essential for setting up a reliable and scalable MKE 4k
environment.

{{< callout type="warning" >}}
To switch to a different CNI following initial CNI installation, you must first
reset your MKE 4k cluster.
{{< /callout >}}

MKE 4k currently supports the following CNI plugins:

- [Calico OSS](configure-cni-providers#calico-oss)

{{< callout type="important" >}}
Calico OSS is the only CNI that is supported for migrating configuration during
an MKE 3 to MKE 4k upgrade.
{{< /callout >}}
