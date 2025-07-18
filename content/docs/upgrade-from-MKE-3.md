---
title: Upgrade from MKE 3.7 or 3.8
weight: 4
---

Comprehensive information is offered herein on how to migrate your existing MKE
3.7 or 3.8 cluster to MKE 4k.

{{< callout type="important" >}}

In the event of upgrade failure, the cluster will rollback to your previous
MKE 3 configuration. Should this occur, Mirantis recommends that you retain the
upgrade logs from the terminal.

{{< /callout >}}

Following a successful upgrade:
- Any swarm workloads will no longer exist.
- For the admin user, the kubeconfig file is present in the `~/.mke/` directory of the machine upon which the ``mkectl`` command was executed. For other users, the admin can [create their kubeconfig files](../getting-started/access-manage-cluster-kubectl/).
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
  The following MKE3 ports are no longer used by MKE 4k and (unless otherwise needed) may be made unavailable on all nodes: [2377,6444,7946,9055,12376,12378,12379,12380,12381,12382,12383,12384,12385,12386,12387,12388,12389,12391,12392,179,12390,2376,443]

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

## Prerequisites

Verify that you have the following components in place before you begin upgrading MKE 3 to MKE 4:

- An MKE cluster running the latest 3.7.x or 3.8.x release:

  ```shell
  kubectl get nodes
  ```

  ```shell
  NAME                                           STATUS   ROLES    AGE     VERSION
  ip-172-31-103-202.us-west-2.compute.internal   Ready    master   7m3s    v1.27.7-mirantis-1
  ip-172-31-104-233.us-west-2.compute.internal   Ready    master   7m3s    v1.27.7-mirantis-1
  ip-172-31-191-216.us-west-2.compute.internal   Ready    <none>   6m59s   v1.27.7-mirantis-1
  ip-172-31-199-207.us-west-2.compute.internal   Ready    master   8m4s    v1.27.7-mirantis-1
  ```

- A backup of the MKE cluster. For comprehensive instruction on how to create
  an MKE 3 back up, refer to [Back up MKE](https://docs.mirantis.com/mke/current/ops/disaster-recovery/back-up-mke.html).

- The latest `mkectl` binary, [installed on your local environment](../getting-started/install-mke-4k-cli):

  ```shell
  mkectl version
  ```

  Example output:

  ```shell
  Version: v4.1.0
  ```

- A `hosts.yaml` file, to provide the information required by `mkectl` to
  connect to each node with SSH.

  Example `hosts.yaml` file:

  ```shell
  cat hosts.yaml
  ```

  ```shell
  hosts:
    - address: <host1-external-ip>
      port: <ssh-port>
      user: <ssh-user>
      keyPath: <path-to-ssh-key>
    - address: <host2-external-ip>
      port: <ssh-port>
      user: <ssh-user>
      keyPath: <path-to-ssh-key>
  ```

- Calico migration to Kubernetes Datastore Driver (KDD) from etcd

  {{< callout type="warning" >}}
  To upgrade successfully to MKE 4k, the source MKE 3 cluster must be configured to use KDD.
  {{< /callout >}}

  To migrate Calico to KDD from etcd:
  1. Obtain the MKE 3 configuration file:
     ```shell
     export MKE_USERNAME=<mke-username>
     export MKE_PASSWORD=<mke-password>
     export MKE_HOST=<mke-fqdn-or-ip-address>
     AUTHTOKEN=$(curl --silent --insecure --data '{"username":"'$MKE_USERNAME'","password":"'$MKE_PASSWORD'"}' https://$MKE_HOST/auth/login | jq --raw-output .auth_token)
     curl --silent --insecure -X GET "https://$MKE_HOST/api/ucp/config-toml" -H "accept: application/toml" -H "Authorization: Bearer $AUTHTOKEN" > mke-config.toml
     ```

  2. In the `cluster_config` section of the MKE 3 configuration file, check the setting of the `calico_kdd` parameter. If it is set to `true`, skip the remaining steps. Otherwise, edit the setting to `true`.

  3. Apply the modified MKE 3 configuration file:
     ```shell
     $ curl --silent --insecure -X PUT -H "accept: application/toml" -H "Authorization: Bearer $AUTHTOKEN" --upload-file 'mke-config.toml' https://$MKE_HOST/api/ucp/config-toml
     ```
     On completion, the following confirmation displays:
     ```shell
       {"message":"Calico datastore upgrade from etcd to kdd successful"}
     ```

  {{< callout type="important" >}}
  - The conversion of the Calico datastore from etcd to
  KDD typically takes about 20 seconds per node, depending on the size of the cluster.
  - According to Tigera, the conversion to KDD freezes cluster networking, and thus new or replacement pods are not able to start. Existing workloads, however, continue to run and their network connectivity are not impacted. 
  - The steps above must be completed as a standalone procedure before beginning the MKE4k upgrade process. The upgrade itself will be covered in the following sections.
  - If your MKE 3 deployment uses an [unmanaged CNI](https://docs.mirantis.com/mke/current/ops/deploy-apps-k8s/install-cni-plugin.html), this upgrade path is not currently supported. 
  - Support for unmanaged CNIs will be introduced in a future version of MKE.  In particular, Calico Enterprise employs Kubernetes as Calico Datastore, and thus the steps detailed herein are not required.
  {{< /callout >}}

## Considerations

Before you upgrade to MKE 4, confirm the existence of a backup of your MKE 3 cluster and review
the [Back up MKE](https://docs.mirantis.com/mke/current/ops/disaster-recovery.html) disaster
recovery documentation for MKE 3.

{{< callout type="info" >}}

The Swarm cluster and all associated artifacts are not included in the upgrade from MKE 3 to MKE 4.

{{< /callout >}}

Back up all non-MKE components separately, making sure to check both manager and worker nodes, as
these are at risk of being deleted rather than migrated during the upgrade to MKE 4.

## Migrate configuration

{{< callout type="info" >}} To upgrade an MKE 3 cluster with GPU enabled,
ensure you complete the [GPU prerequisites](../configuration/nvidia-gpu/#prerequisites) before
starting the upgrade process. {{< /callout >}}

### Kubernetes Custom Flags

MKE 3 and MKE 4 both support the application of additional flags to Kubernetes components that have the following fields in the MKE configuration file, each specified as a list of strings:

```
custom_kube_api_server_flags
custom_kube_controller_manager_flags
custom_kubelet_flags
custom_kube_scheduler_flags
custom_kube_proxy_flags
```

MKE 4 supports an `extraArgs` field for each of these components, though, which accepts a map of key-value pairs. During upgrade from MKE 3, MKE 4 converts these custom flags to the corresponding `extraArgs` field. Any flags that cannot be automatically converted are listed in the upgrade summary.

Example of custom flags conversion:

- MKE 3 configuration file:

  ```
  [cluster_config.custom_kube_api_server_flags] = ["--enable-garbage-collector=false"]
  ```

- MKE 4 configuration file:

  ```
  spec:
    apiServer:
      extraArgs:
        enable-garbage-collector: false
  ```

### Kubelet Custom Flag Profiles

MKE 3 supports a map of kubelet flag profiles to specific nodes using the `custom_kubelet_flags_profiles` setting in the toml configuration file.

MKE 4 does not support kubelet flag profiles, but you can use [Kubelet custom profiles](../configuration/kubernetes/kubelet.md#kubelet-custom-profiles) to map `KubeletConfiguration` values to specific nodes. MKE 4 does support the migration of MKE 3 kubelet flag profiles to kubelet custom profiles.

The conversion of flags to `KubeletConfiguration` values is best-effort, and any flags that cannot be
converted are listed in the upgrade summary. Hosts with a custom flag profile label are marked for the
corresponding kubelet custom profile.

## Perform the migration

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

The external address is the domain name of the load balancer. For details,
see [System requirements: Load balancer
requirements](../getting-started/system-requirements#load-balancer-requirements).

The `--config-out` flag allows you to specify a path where the MKE 4k configuration
file will be automatically created and saved during upgrade. If not specified,
the configuration file prints to your console on completion. In this case, save
the output to a file for future reference

The upgrade process requires time to complete. Once the process is complete,
run the following command to verify that the MKE 4k cluster is operating:

```shell
sudo k0s kc get nodes
```

Example output:

```shell
NAME                                           STATUS   ROLES           AGE   VERSION
ip-172-31-111-4.us-west-1.compute.internal     Ready    control-plane   45h   v1.31.2+k0s
ip-172-31-216-253.us-west-1.compute.internal   Ready    <none>          45h   v1.31.2+k0s
```

{{< callout type="info" >}}

The MKE 3 cluster will no longer be accessible through the previously created
client bundle. The docker swarm cluster will no longer be accessible as well.

{{< /callout >}}

### Offline upgrade

To perform an offline upgrade from MKE 3 to MKE 4k, [prepare your environment
as described in Offline
installation](../getting-started/offline-installation/#preparation), and add
the following flags to the `mkectl upgrade` command:

* `--image-registry=<registry_full_path>`
* `--chart-registry=oci://<registry_full_path>`
* `--mke3-airgapped=true`

| Setting                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--image-registry` | Sets your registry address with a project path that contains your MKE 4k images. For example, `private-registry.example.com:8080/mke`. <br><br>The setting must not end with a slash `/`.<br><br>The port is optional.                                                                                                                                                                                                                                                                   |
| `--chart-registry` | Sets your registry address with a project path that contains your MKE 4k helm charts in OCI format. For example, `oci://private-registry.example.com:8080/mke`.<br><br>The setting must always start with `oci://`, and it must not end with a slash `/` .<br><br>If you uploaded the bundle as previously described, the registry address and path will be the same for chart and image registry, with the only difference being the `oci://` prefix in the chart registry URL. |
| `--mke3-airgapped=true`        | Indicates that your environment is airgapped.                                                                                                                                                                                                                                                                                                                   |

### Migration failure

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

## Revert the upgrade

To revert a cluster upgraded to MKE 4k back to MKE 3:

1. [Uninstall MKE 4](../getting-started/uninstall-cluster).

2. [Restore MKE 3 from a backup](https://docs.mirantis.com/mke/current/ops/disaster-recovery.html).


## RBAC upgrades

As MKE 4k does not support Swarm mode, the platform uses standard [Kubernetes
RBAC authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).
As such, the Swarm authorization configuration that is in place for MKE 3 is not present in MKE 4.

### Groups

To enable the same RBAC hierarchy as in MKE 3 with `orgs` and `teams` groups, but
without the two-level limitation, MKE 4k replaces `orgs` and `teams` with
the Kubernetes `AggregatedRoles`.

**Authorization structure comparison:**

```
MKE 3:                           MKE 4:

├── entire-company (org)         ├── entire-company-org (AggregatedRole)
│   ├── development (team)       ├── development-team (AggregatedRole)
│   │   ├── bob (user)           │   ├── bob (user)
│   ├── production (team)        ├── production-team (AggregatedRole)
│   │   ├── bob (user)│          │   ├── bob (user)
│   │   ├── bill (user)          │   ├── bill (user)
│   ├── sales (team)             ├── sales-team (AggregatedRole)
```

### Roles

Roles are bound to the aggregated roles for integration into the org, team, and user structure.
Thus, what was previously an organization or a team role will have `-org` or `-team`
appended to its name.

A role can be assigned at any level in the hierarchy, with its permissions granted to all members
at that same level.

**Example organization binding:**

```
├── entire-company-org (AggregatedRole) -- entire-company-org (RoleBinding) -- view (Role)
│   ├── development-team (AggregatedRole)
│   │   ├── bob (user)
│   ├── production-team (AggregatedRole)
│   │   ├── bob (user)
│   │   ├── bill (user)
│   ├── sales-team (AggregatedRole)
```

In the example above, all members of the `entire-company` org group have
`view` permissions. This includes the `development-team`,
`production-team`, `sales-team`, `bob`, and `bill`.

**Example team binding:**

```
│   ├── development:team (AggregatedRole) -- development:team (RoleBinding) -- edit (Role)
│   │   ├── bob (user)
```

In the example above, the binding grants `edit` permissions only to the
members of the development team, which only includes `bob`.

{{< callout type="warning" >}}

Swarm roles are partially translated to Kubernetes roles. During upgrade,
any detected Swarm role is replicated without permissions, thus
preserving the org/team/user structure.
If no Swarm roles are detected, a `none` role is created as a placeholder,
as Kubernetes requires each aggregated role to have at least one role.
This `none` role has no permissions, with its only purpose being to maintain
structural integrity.

{{< /callout >}}

## CoreDNS Lameduck upgrade

MKE 4k supports upgrading from MKE 3 systems that are running with CoreDNS and
Lameduck enabled. Refer
to the table below for a comparison of the CoreDNS Lameduck configuration
parameters between MKE 3 and MKE 4:

| MKE 3                                              | MKE 4k                 |
| -------------------------------------------------- | --------------------- |
| [cluster_config.core_dns_lameduck_config.enabled]  | dns.lameduck.enabled  |
| [cluster_config.core_dns_lameduck_config.duration] | dns.lameduck.duration |

## Troubleshoot the upgrade

You can address various potential MKE upgrade issues using the tips and
suggestions detailed herein.

{{< callout type="important" >}}

The MKE 3 `etcdv3` backend is not supported for upgrade to MKE 4k.

{{< /callout >}}

During the upgrade from MKE 3 to MKE 4, which defaults to the `etcdv3`
backend, you may receive the following error:

```bash
mkectl upgrade --hosts-path hosts.yaml --mke3-admin-username admin --mke3-admin-password <mke_admin_password> -l debug --config-out new-mke4.yaml --external-address <mke4_external_address>
...
Error: unable to generate upgrade config: unsupported configuration for mke4 upgrade: mke3 cluster is using etcdv3 and not kdd backend for calico
```

To resolve the issue, ensure that:

- The MKE 3 source is the latest 3.7.x or 3.8.x release.
- The `calico_kdd` flag in the MKE 3 configuration file is set to `true`.
- The configuration is applied to the MKE 3 cluster.

{{< callout type="info" >}}

A KDD mode upgrade is irreversible. Thus, to reduce risk, when upgrading
enterprise clusters, it is recommended that you work directly with Mirantis to
plan the process and monitor it through to completion.

{{< /callout >}}

Example output:

```bash
$ AUTHTOKEN=$(curl --silent --insecure --data '{"username":"'$MKE_USERNAME'","password":"'$MKE_PASSWORD'"}' https://$MKE_HOST/auth/login | jq --raw-output .auth_token)

$ curl --silent --insecure -X PUT -H "accept: application/toml" -H "Authorization: Bearer $AUTHTOKEN" --upload-file 'mke-config.toml' https://$MKE_HOST/api/ucp/config-toml
{"message":"Calico datastore upgrade from etcd to kdd successful"}
```

{{< callout type="important" >}}

To migrate an etcd-backed cluster to KDD, contact Mirantis support.

{{< /callout >}}
