---
title: Upgrade Considerations
weight: 1
---

Before you upgrade to MKE 4k, confirm the existence of a backup of your MKE 3
cluster and review the [Back up
MKE](https://docs.mirantis.com/mke/current/ops/disaster-recovery.html) disaster
recovery documentation for MKE 3.

{{< callout type="info" >}}

The Swarm cluster and all associated artifacts are not included in the upgrade from MKE 3 to MKE 4k.

{{< /callout >}}

Back up all non-MKE components separately, making sure to check both manager
and worker nodes, as these are at risk of being deleted rather than migrated
during the upgrade to MKE 4k.
