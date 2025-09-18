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

Currently, MKE 4k directly supports the [Calico
OSS](configure-cni-providers#calico-oss) CNI plugin.

You can configure other CNI solutions for use with MKE 4k. Be aware, though,
that Mirantis offers only [limited basic instruction for how to configure
unmanaged CNIs for use with MKE 4k](unmanaged-cni). Thus, for detailed
information on whichever unmanaged CNI plugins you intend to deploy, you must
refer to the product documentation or support service.

{{< callout type="warning" >}}
To switch to a different CNI following initial CNI installation, you must first
reset your MKE 4k cluster.
{{< /callout >}}