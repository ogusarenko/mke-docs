---
title: Upgrade Prerequisites
weight: 2
---

Verify that you have the following components in place before you begin
upgrading MKE 3 to MKE 4k:

- An eligible MKE 3 cluster:

  - MKE 3.7.15 or later
  - Any MKE 3.8 release

- A backup of the MKE cluster. For comprehensive instruction on how to create
  an MKE 3 back up, refer to [Back up MKE](https://docs.mirantis.com/mke/current/ops/disaster-recovery/back-up-mke.html).

- The latest `mkectl` binary, [installed on your local environment](../../getting-started/install-mke-4k-cli):

  ```shell
  mkectl version
  ```

  Example output:

  ```shell
  Version: v4.1.0
  ```

- A hosts.yaml file, prepared as follows:

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
  {{< callout type="important" >}}

  - Confirm that all nodes are accessible via SSH, using the specified
    credentials.
  - Use either external IP addresses or FQDNs. These must be resolvable and
  reachable from the system from which you are running mkectl. Mirantis
  recommends that you use a load balancer is recommended for high-availability
  setups.
  - It is not necessary to declare a node role in the `hosts.yaml` file as they are
  automatically detected during upgrade.

  {{< /callout >}}

<a id="calico-migration-kdd-etcd">

</a>- Calico migration to Kubernetes Datastore Driver (KDD) from etcd

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
  - According to Tigera, the conversion to KDD freezes cluster networking, and thus new or replacement pods are not able to start. Existing workloads, however, continue to run and their network connectivity is not impacted.
  - The steps above must be completed as a standalone procedure before beginning the MKE4k upgrade process. The upgrade itself will be covered in the following sections.
  - If your MKE 3 deployment uses an [unmanaged CNI](https://docs.mirantis.com/mke/current/ops/deploy-apps-k8s/install-cni-plugin.html), this upgrade path is not currently supported.
  - Support for unmanaged CNIs will be introduced in a future version of MKE.  In particular, Calico Enterprise employs Kubernetes as Calico Datastore, and thus the steps detailed herein are not required.
  {{< /callout >}}

- k0rdent is not enabled when you upgrade from MKE 3 to MKE 4k. If
  you would like to enable it following the upgrade process, set
  ``k0rdent.enabled`` to ``true`` in the `mke4.yaml` configuration file.

  ```yaml
  spec:
  k0rdent:
    enabled: true
  ```