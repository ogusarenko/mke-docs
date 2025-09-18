---
aliases:
  - /latest/tutorials/mke4k-child-clusters/deploy-mke4k-child-cluster/
  - /docs/tutorials/mke4k-child-clusters/deploy-mke4k-child-cluster/
title: Deploy an MKE 4k child cluster
weight: 6
---

The instructions herein assume that you are familiar with the k0rdent platform
and the basics of how to deploy child clusters.

1. Prepare the child cluster configuration. Set the required MKE version, cloud
   provider, region, and the name of the installed k0rdent Credential.

   Example: Basic AWS MKE 4k child cluster:

   ```yaml
   apiVersion: mke.mirantis.com/v1alpha1
   kind: MkeChildConfig
   metadata:
     name: my-mke-child
     namespace: k0rdent
   spec:
     version: v4.1.1
     infrastructure:
       provider: aws
       credential: aws-cluster-identity-cred
       region: eu-west-3
   ```

2. Enable the k0rdent CAPI providers for the specified cloud provider.

   <!-- In 4.1.2 the `.spec.k0rdent.providers` field will be removed from the `MkeConfig` and all the CAPI providers from k0rdent release will be enabled by default and user will not need to worry about enabling them. -->

   To check whether providers are already enabled or to get the list of
   required providers, make an attempt to apply the `MkeChildConfig` object
   you created:

   ```
   kubectl apply -f path/to/mkechild.yaml
   ```

   If the providers are missing, the configuration will be rejected with the
   list of provider names. For example:

   ```
   The MkeChildConfig "my-mke-child" is invalid: spec.infrastructure.provider: Invalid value: "aws": one or more required k0rdent providers are not deployed yet: [cluster-api-provider-aws cluster-api-provider-k0sproject-k0smotron]
   ```

   To enable k0rdent providers, you must add the providers from the error you
   received to the `mke4.yaml` configuration file of the mothership cluster.
   Be aware that the providers require a few minutes to start.

   ```yaml
   spec:
     k0rdent:
       enabled: true
       providers:
         - name: cluster-api-provider-aws
         - name: cluster-api-provider-k0sproject-k0smotron
   ```

3. Apply the edited `mke4.yaml` configuration file.

   ```
   mkectl apply -f mke4.yaml
   ```

4. Prepare and install the k0rdent Credential object for the cloud provider
   that you will use with the `MkeChildConfig` object. Refer to the official 
   k0rdent documentation, [The process](
   https://docs.k0rdent-enterprise.io/v1.1.0/admin/access/credentials/credentials-process/)

5. Apply the child cluster:

   ```
   kubectl apply -f path/to/mkechild.yaml
   ```

6. Check the created object:

   ```
   kubectl -n k0rdent get mkechildconfig
   ```

   Example output:
   ```
   NAME                   AGE
   my-mke-child           1m
   ```

   The `ClusterDeployment` for the child cluster will be created, with the
   same name as `MkeChildConfig`. This can require some time the first time
   you attempt to create `ClusterDeployment`, as the required cluster and
   service templates need to be downloaded.

7. Confirm that `ClusterDeployment` for the child cluster is ready.

   ```
   kubectl -n k0rdent get clusterdeployment my-mke-child
   ```

   Example output:
   ```
   NAME                       READY   SERVICES   TEMPLATE                MESSAGES          AGE
   my-mke-child               True    10/10      mke4k-aws-1-1-2         Object is ready   1m
   ```

8. View the status of the applied `MkeChildConfig` to obtain the name of the
   secret that contains the kubeconfig:

   ```
   kubectl -n k0rdent get mkechildconfig my-mke-child -o yaml
   ```

   Example status, with secret:

   ```...
   status:
     clusterID: kube-system:20d005f3-d8c5-4c88-a8d9-7e97e6a1a8ca
     kubeConfigSecret: my-mke-child-kubeconfig
     licenseStatus:
       licenseType: unlicensed
   ```

9. Get the kubeconfig and use it to connect to the child cluster:

   ```
   kubectl -n k0rdent get secret my-mke-child-kubeconfig -o jsonpath='{ .data.value }' | base64 -d > kubeconfig.yaml
   ```

10. Use the obtained kubeconfig to connect to the cluster:

   ```
   KUBECONFIG=kubeconfig.yaml kubectl get no
   ```

   Example output:

   ```
   NAME                                        STATUS   ROLES           AGE    VERSION
   ip-172-31-0-32.us-east-2.compute.internal   Ready    control-plane   3h5m   v1.32.6+k0s
   ip-172-31-0-49.us-east-2.compute.internal   Ready    <none>          3h5m   v1.32.6+k0s
   ...
   ```

{{< callout type=“warning >}}

- If you decide to use the `mkectl reset` command to wipe the standalone MKE
  cluster that is serving as the mothership cluster, you should delete all the
  existing child clusters as otherwise they will remain in an unmanaged state.

- Currently, `MkeChildConfig` objects can only be applied to a
  `k0rdent` namespace.

{{< /callout >}}
