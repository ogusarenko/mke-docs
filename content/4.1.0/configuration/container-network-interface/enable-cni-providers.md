---
aliases:
  - /latest/configuration/container-network-interface/enable-cni-providers/
  - /docs/configuration/container-network-interface/enable-cni-providers/
title: Enable CNI Providers
weight: 3
---

To enable a Container Network Interface for MKE 4k:

1. Obtain the default `mke4.yaml` configuration file:

   ```
   mkectl init
   ```

2. In the `providers` section of the `mke4.yaml` configuration file, set the
   `enabled` parameter for the CNI you want to deploy to `true` and the
   `enabled` parameter for the CNIs you do not want to deploy to `false`.

3. Apply the configuration:

   ```
   mkectl apply -f mke4.yaml
   ```

4. Verify the successful deployment of your chosen CNI in the MKE 4k cluster.<br><br>

   <details>

   <summary><b>To verify Calico OSS CNI</b></summary>

   Run the following command:

   ```
   k0s kc get po --show-labels -A|grep -i -e tigera -e calico
   ```

   Example output:

   ```
   calico-apiserver   calico-apiserver-5f6bbbcd-9lqsk                              1/1     Running   0          34m   apiserver=true,app.kubernetes.io/name=calico-apiserver,k8s-app=calico-apiserver,pod-template-hash=5f6bbbcd
   calico-apiserver   calico-apiserver-5f6bbbcd-mxqq7                              1/1     Running   0          34m   apiserver=true,app.kubernetes.io/name=calico-apiserver,k8s-app=calico-apiserver,pod-template-hash=5f6bbbcd
   calico-system      calico-kube-controllers-64764dc585-xlcl7                     1/1     Running   0          34m   app.kubernetes.io/name=calico-kube-controllers,k8s-app=calico-kube-controllers,pod-template-hash=64764dc585
   calico-system      calico-node-cx452                                            1/1     Running   0          33m   app.kubernetes.io/name=calico-node,controller-revision-hash=c9788bcc,k8s-app=calico-node,pod-template-generation=2
   calico-system      calico-node-lfwrv                                            1/1     Running   0          33m   app.kubernetes.io/name=calico-node,controller-revision-hash=c9788bcc,k8s-app=calico-node,pod-template-generation=2
   calico-system      calico-typha-658d6d7f94-f54t7                                1/1     Running   0          34m   app.kubernetes.io/name=calico-typha,k8s-app=calico-typha,pod-template-hash=658d6d7f94
   calico-system      csi-node-driver-8q2g8                                        2/2     Running   0          34m   app.kubernetes.io/name=csi-node-driver,controller-revision-hash=6545d9b9d5,k8s-app=csi-node-driver,name=csi-node-driver,pod-template-generation=1
   calico-system      csi-node-driver-nsdgr                                        2/2     Running   0          34m   app.kubernetes.io/name=csi-node-driver,controller-revision-hash=6545d9b9d5,k8s-app=csi-node-driver,name=csi-node-driver,pod-template-generation=1
   tigera-operator    tigera-operator-588c6fd5d4-wr5xc                             1/1     Running   0          34m   k8s-app=tigera-operator,name=tigera-operator,pod-template-hash=588c6fd5d4
   ```

   </details>
