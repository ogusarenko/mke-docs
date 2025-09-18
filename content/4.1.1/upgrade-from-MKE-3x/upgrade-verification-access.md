---
aliases:
  - /latest/upgrade-from-MKE-3x/upgrade-verification-access/
  - /docs/upgrade-from-MKE-3x/upgrade-verification-access/
title: Upgrade Verification and Access
weight: 5
---

{{< callout type="info" >}}

Typical upgrade durations, determined through controlled testing in an AWS
environment\*:

| Node Configuration | Detail                | Duration         |
|--------------------|-----------------------|------------------|
| 5-node cluster     | 3 managers, 2 workers | 10:19.87 minutes |
| 10-node cluster    | 3 managers, 7 workers | 11:26.64 minutes |

These estimates are offered for general guidance, however, as actual upgrade
durations will vary based on hardware performance (CPU/memory/disk), workload
density, network throughput, and storage backend performance. For precise
planning purposes, Mirantis strongly recommends that you run a test upgrade
in a staging environment that mirrors your production specifications.

\* Ubuntu 22.04 LTS, manager and worker nodes (m5.2xlarge: 8 vCPU, 32GB RAM)

{{< /callout >}}

On completion of the ``mkectl upgrade`` command, a kubeconfig file for the default admin user is generated and stored at `~/.mke/mke.kubeconf`.

Set a `KUBECONFIG` environment variable.

```bash
export KUBECONFIG=~/.mke/mke.kubeconf
```

Next, verify the MKE 4k cluster node readiness, cluster health, and workload status::

1. Verify node readiness:

   ```bash
   kubectl get nodes
   ```

   - Healthy nodes should report `STATUS=Ready`.

     ```bash
     kubectl describe node <node-name> | grep -i conditions: -A 10
     ```

   - Confirm the following conditions:

     - `Ready=True`
     - `MemoryPressure/NetworkUnavailable/DiskPressure=False`

2. Verify workload status:

   ```bash
   kubectl get pods --all-namespaces
   ```

   - Check columns for `STATUS=Running` and `READY`

     ```bash
     kubectl get deployments,statefulsets --all-namespaces
     ```

   - Confirm that `AVAILABLE` matches `DESIRED` replicas.

3. Review the logs:

   ```bash
   kubectl get pods -n mke                # MKE namespace is mke
   kubectl logs <pod-name> -n mke         # Check logs for mke system pods
   kubectl logs <pod-name> -n <namespace> # Or any other application pods
   ```

4. Verify cluster health:

   ```bash
   kubectl top nodes # Resource usage
   kubectl top pods -A

{{< callout type="info" >}}

The MKE 3 cluster will no longer be accessible through the previously created
client bundle. The docker swarm cluster will no longer be accessible as well.

{{< /callout >}}

