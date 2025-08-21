---
title: Upgrade the Configuration
weight: 3
---

{{< callout type="info" >}} To upgrade an MKE 3 cluster with GPU enabled,
ensure you complete the [GPU prerequisites](../../configuration/nvidia-gpu/#prerequisites) before
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

MKE 4 does not support kubelet flag profiles, but you can use [Kubelet custom profiles](../../configuration/kubernetes/kubelet#kubelet-custom-profiles) to map `KubeletConfiguration` values to specific nodes. MKE 4 does support the migration of MKE 3 kubelet flag profiles to kubelet custom profiles.

The conversion of flags to `KubeletConfiguration` values is best-effort, and any flags that cannot be
converted are listed in the upgrade summary. Hosts with a custom flag profile label are marked for the
corresponding kubelet custom profile.
