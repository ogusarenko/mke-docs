---
title: Perform the Upgrade
weight: 4
---

An upgrade from MKE 3 to MKE 4k consists of the following steps, all of which
are performed through the use of the `mkectl` tool:

- Run pre-upgrade checks to verify the upgradability of the cluster.
- Carry out pre-upgrade migrations to prepare the cluster for an upgrade from
  a hyperkube-based MKE 3 cluster to a k0s-based MKE 4k cluster.
- Upgrade manager nodes to k0s.
- Upgrade worker nodes to k0s.
- Carry out post-upgrade cleanup to remove MKE 3 components.
- Output the new `mke4.yaml` configuration file.

To upgrade an MKE 3 cluster, use the `mkectl upgrade` command:

```shell
mkectl upgrade --hosts-path <path-to-hosts-yaml> \
  --mke3-admin-username <admin-username> \
  --mke3-admin-password <admin-password> \
  --external-address <external-address>\
  --config-out <path-to-desired-file-location>
```

The `--config-out` flag allows you to specify a path where the MKE 4k configuration
file will be automatically created and saved during upgrade. If not specified,
the configuration file prints to your console on completion. In this case, save
the output to a file for future reference

### Offline upgrade

To perform an offline upgrade from MKE 3 to MKE 4k, [prepare your environment
as described in Offline
installation](../../getting-started/offline-installation/#preparation), and add
the following flags to the `mkectl upgrade` command:

* `--image-registry=<registry_full_path>`
* `--chart-registry=oci://<registry_full_path>`
* `--mke3-airgapped=true`

| Setting                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--image-registry` | Sets your registry address with a project path that contains your MKE 4k images. For example, `private-registry.example.com:8080/mke`. <br><br>The setting must not end with a slash `/`.<br><br>The port is optional.                                                                                                                                                                                                                                                                   |
| `--chart-registry` | Sets your registry address with a project path that contains your MKE 4k helm charts in OCI format. For example, `oci://private-registry.example.com:8080/mke`.<br><br>The setting must always start with `oci://`, and it must not end with a slash `/` .<br><br>If you uploaded the bundle as previously described, the registry address and path will be the same for chart and image registry, with the only difference being the `oci://` prefix in the chart registry URL. |
| `--mke3-airgapped=true`        | Indicates that your environment is airgapped.                                                                                                                                                                                                                                                                                                                   |

### Upgrade failure

In the event of an upgrade failure, the upgrade process rolls back,
restoring the MKE 3 cluster to its original state.

{{< details title="Example: Rollback output" closed="true" >}}

```shell
WARN[0096] Initiating rollback because of upgrade failure. upgradeErr = aborting upgrade due to signal interrupt
INFO[0096] Initiating rollback of MKE to version: 3.7.15
INFO[0096] Step 1 of 2: [Rollback Upgrade Tasks]
INFO[0096] Resetting k0s using k0sctl ...
INFO[0106] ==> Running phase: Connect to hosts
INFO[0106] [ssh] 54.151.30.20:22: connected
INFO[0106] [ssh] 54.215.145.126:22: connected
INFO[0106] ==> Running phase: Detect host operating systems
INFO[0106] [ssh] 54.151.30.20:22: is running Ubuntu 22.04.5 LTS
INFO[0106] [ssh] 54.215.145.126:22: is running Ubuntu 22.04.5 LTS
INFO[0106] ==> Running phase: Acquire exclusive host lock
INFO[0107] ==> Running phase: Prepare hosts
INFO[0107] ==> Running phase: Gather host facts
INFO[0107] [ssh] 54.151.30.20:22: using ip-172-31-8-69.us-west-1.compute.internal as hostname
INFO[0107] [ssh] 54.215.145.126:22: using ip-172-31-48-46.us-west-1.compute.internal as hostname
INFO[0107] [ssh] 54.151.30.20:22: discovered ens5 as private interface
INFO[0107] [ssh] 54.215.145.126:22: discovered ens5 as private interface
INFO[0107] [ssh] 54.151.30.20:22: discovered 172.31.8.69 as private address
INFO[0107] [ssh] 54.215.145.126:22: discovered 172.31.48.46 as private address
INFO[0107] ==> Running phase: Gather k0s facts
INFO[0108] [ssh] 54.215.145.126:22: found existing configuration
INFO[0108] [ssh] 54.215.145.126:22: is running k0s controller+worker version v1.31.1+k0s.1
WARN[0108] [ssh] 54.215.145.126:22: the controller+worker node will not schedule regular workloads without toleration for node-role.kubernetes.io/master:NoSchedule unless 'noTaints: true' is set
INFO[0108] [ssh] 54.215.145.126:22: listing etcd members
INFO[0110] [ssh] 54.151.30.20:22: is running k0s worker version v1.31.1+k0s.1
INFO[0110] [ssh] 54.215.145.126:22: checking if worker ip-172-31-8-69.us-west-1.compute.internal has joined
INFO[0110] ==> Running phase: Reset workers
INFO[0111] [ssh] 54.151.30.20:22: reset
INFO[0111] ==> Running phase: Reset controllers
INFO[0114] [ssh] 54.215.145.126:22: reset
INFO[0114] ==> Running phase: Reset leader
INFO[0114] [ssh] 54.215.145.126:22: reset
INFO[0114] ==> Running phase: Reload service manager
INFO[0114] [ssh] 54.151.30.20:22: reloading service manager
INFO[0114] [ssh] 54.215.145.126:22: reloading service manager
INFO[0115] ==> Running phase: Release exclusive host lock
INFO[0115] ==> Running phase: Disconnect from hosts
INFO[0115] ==> Finished in 8s
INFO[0125] Running etcd cleanup service ...
INFO[0128] MKE3 etcd directories successfully cleaned up ...
INFO[0128] Restoring etcd from the previously taken system backup ...
INFO[0128] Successfully restored etcd with output:
INFO[0128] Deploying etcd server ...
INFO[0129] Successfully deployed the etcd server with output: fb09c3e5e514d9ffe03a3df4bc461c29a695cf73d703ace5294702b7023021af
INFO[0129] Waiting for etcd to be healthy ...
INFO[0131] etcd health check succeeded!
INFO[0178] [Rollback Upgrade Tasks] Completed
INFO[0178] Step 2 of 2: [Rollback Pre Upgrade Tasks]
INFO[0178] [Rollback Pre Upgrade Tasks] Completed
INFO[0178] Rollback to MKE version 3.7.15 completed successfully ...
FATA[0178] Upgrade failed due to error: aborting upgrade due to signal interrupt
```

{{< /details >}}

{{< callout type="info" >}}

A failed upgrade can leave behind an empty or corrupted `~/.mke/mke.kubeconf` file, which will block any subsequent upgrade attempts. To resolve this issue, manually delete the file:
```bash
rm -f ~/.mke/mke.kubeconf
```
Following this action, during the next upgrade, the system will automatically generate a valid configuration.

{{< /callout >}}
