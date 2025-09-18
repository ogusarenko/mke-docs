---
title: Upgrade from MKE 3.7 or 3.8
weight: 4
---

{{< callout type="warning" >}}

Only MKE 3.7.15+ and MKE 3.8.x versions can be upgraded to MKE 4k. Mirantis
recommends that before you perform the upgrade, you first upgrade to the latest
3.7.15+ or 3.8.x release.

{{< /callout >}}

{{< callout type="important" >}}

In the event of upgrade failure, the cluster will rollback to your previous
MKE 3 configuration. Should this occur, Mirantis recommends that you retain the
upgrade logs from the terminal.

{{< /callout >}}

Comprehensive information is offered herein on how to upgrade your existing MKE
3.7 or 3.8 cluster to MKE 4k.

Following a successful upgrade:
- Any swarm workloads will no longer exist.
- For the admin user, the kubeconfig file is present in the `~/.mke/` directory of the machine upon which the `mkectl` command was executed. For other users, the admin can [create their kubeconfig files](../getting-started/access-manage-cluster-kubectl/).
- The UCP Controller API will no longer be active or supported, and thus the
  MKE 3 client bundle will become invalid for MKE 4k.
- The terminal prints a summary of the process.

  {{< details title="Example: Upgrade Summary" closed="true" >}}

  ```
  Upgrade Summary
  ---------------

  Configuration file
  ---------------
  All MKE 3 TOML settings were successfully converted into MKE 4 YAML settings.

  Authentication
  ---------------
  Users found in the MKE3 cluster: 1

  All users were upgraded to the MKE 4k cluster successfully.

  Authorization
  ---------------
  Organizations found in the MKE3 cluster: 1
  Teams found in the MKE3 cluster: 0

  All organizations and teams were translated to aggregated roles in the MKE 4k cluster successfully.

  Ports
  ---------------
  The following MKE 3 ports are no longer used by MKE 4k and (unless otherwise needed) may be made unavailable on all nodes: [2377,6444,7946,9055,12376,12378,12379,12380,12381,12382,12383,12384,12385,12386,12387,12388,12389,12391,12392,179,12390,2376,443]

  MCR
  ---------------
  MCR may safely be uninstalled

  MKE3 Cleanup
  ---------------
  MKE 3.8.5 was uninstalled from the cluster successfully.
  ```

  {{< /details >}}

{{< callout type="warning" >}}

Your MKE 3 system will be completely unavailable during the upgrade process. Cluster access will be restored only once the upgrade is complete.

{{< /callout >}}

The upgrade period depends on the size of your cluster. You can track the
progress of your upgrade by way of the terminal, which displays step-by-step
logs on the current state of upgrade.

