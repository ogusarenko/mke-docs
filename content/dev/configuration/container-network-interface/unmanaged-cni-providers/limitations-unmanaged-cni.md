---
title: Limitations
weight: 1
---

Prior to installing an unmanaged CNI plugin for use with MKE 4k, you should be
familiar with the limitations imposed by the product:

- MKE 4k does not manage the version or configuration of third-party CNIs. As
  such, you will need to handle upgrades to such CNIs separately from MKe 4k
  upgrades.

- Switching between a managed CNI and an unmanaged CNI on a running MKE 4k
  cluster is not supported by MKE 4k.
