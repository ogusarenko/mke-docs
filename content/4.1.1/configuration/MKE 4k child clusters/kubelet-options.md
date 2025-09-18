---
aliases:
  - /latest/configuration/MKE 4k child clusters/kubelet-options/
  - /docs/configuration/MKE 4k child clusters/kubelet-options/
title: Kubelet options
weight: 7
---

Common configuration file section: `.spec.kubelet`

The `MkeChildConfig` configuration file offers additional fields,
`managerProfile` and `workerProfile`, which you can use to assign custom
profiles to manager and worker nodes. The default value for `managerProfile`
is `mke-default-manager`, and the default value for `workerProfile` is
`mke-default-worker`.

To specify a specific name for `managerProfile` and `workerProfile` the
name must be on the list under `customProfiles`.

Example:

```yaml
spec:
  kubelet:
    workerProfile: <worker_profile_name_>
```

Refer to [kubelet](../../../configuration/kubernetes/kubelet) for more details.
