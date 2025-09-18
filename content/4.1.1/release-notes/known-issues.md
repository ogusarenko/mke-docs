---
aliases:
  - /latest/release-notes/known-issues/
  - /docs/release-notes/known-issues/
title: Known issues
weight: 4
---

The MKE 4k known issues with available workarounds are described herein.

<!--- [BOP-2046] -->

## Post-install kubelet parameter modifications require k0s restart

Modifications made to the kubelet parameters in the `mke4.yaml` configuration
file after the initial MKE 4k installation require a restart of k0s on every
cluster node. To do this:

1. Wait for a short time, roughly 60 seconds after the application of the
   `mkectl apply` command, to give the Pods time to enter their `Running` state.

2. Run the `systemctl restart k0scontroller` command on all manager nodes and
   the  `systemctl restart k0scontroller` command on all worker nodes.

<!--- [BOP-2030] -->

## Upgrade may fail on clusters with two manager nodes

MKE 3 upgrades to MKE 4k may fail on clusters that have only two manager nodes.

{{< callout type="info" >}}

Mirantis does not sanction upgrading MKE 3 clusters that have an even number of
manager nodes. In general, having an even number of manager nodes is avoided in
clustering systems due to quorum and availability factors.

{{< /callout >}}

<!--- [BOP-898][BOP-899] -->

## Calico eBPF and IPVS modes are not supported

Calico eBPF and IPVS mode are not yet supported for MKE 4k. As such, upgrading
from an MKE 3 cluster using either of those networking modes results in an
error:

```sh
FATA[0640] Upgrade failed due to error: failed to run step [Upgrade Tasks]:
unable to install BOP: unable to apply MKE4 config: failed to wait for pods:
failed to wait for pods: failed to list pods: client rate limiter Wait returned
an error: context deadline exceeded
```

<!--- [BOP-964] -->

## mke-operator in crashloopbackoff status

The mke-operator-controller-manager is in crashloopbackoff status in MKE 4k
Alpha 2. You can safely ignore this, however, as it has no effect on MKE
4.0.0-alpha.2.0 functionality.

<!--- [BOP-891] -->

## Upgrade to MKE 4k fails if kubeconfig file is present in source MKE 3.x

Upgrade to MKE 4k fails if the `~/.mke/mke.kubeconf` file is present in the
source MKE 3.x system.

**Workaround:**

Make a backup of the old `~/.mke/mke.kubeconf` file and then delete it.

## `reset` command must be run with --force flag

You must run the `reset` command with the `--force` flag, as without this flag
the command will always return an error.

```
mkectl reset -f mke4.yaml
```

Example output:

```
time="2025-09-08T19:35:44-04:00" level=info msg="==> Running phase: Disconnect from hosts"
Error: reset requires --force
```

## `restore` command output sometimes hangs

The command output for a restore operation sometimes hangs, even though the
underlying restore operation is a success.

**Workaround:**

Run the restore operation with a large timeout, such as `--timeout 1800s`.