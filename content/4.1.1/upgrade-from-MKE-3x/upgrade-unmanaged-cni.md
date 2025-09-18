---
aliases:
  - /latest/upgrade-from-MKE-3x/upgrade-unmanaged-cni/
  - /docs/upgrade-from-MKE-3x/upgrade-unmanaged-cni/
title: Upgrade with unmanaged CNI
weight: 10
---

In an upgrade to MKE 4k of an MKE 3.x cluster that is running an unmanaged CNI
to MKE 4k, all workloads are carried over, including those that are used by the
unmanaged CNI to establish networking capability.

As is the case with a managed CNI scenario, the upgrade process for an
unmanaged CNI allows a set length of time for networking to be established.

{{< callout type=info >}}

For information on how to configure the time span for initiating cluster
networking, as well as the total time period allowed for the full upgrade
process, refer to [Configure time windows for network
bootstrapping](../../configuration/container-network-interface/unmanaged-cni-providers/configure-time-windows-bootstrapping).

{{< /callout >}}

Once the unmanaged CNI has verified that cluster networking is in place, the
upgrade will immediately proceed. If, however, that verification cannot be made
in the allotted time, the upgrade process will fail and rollback to the
original MKE 3.x cluster will occur.
