---
aliases:
  - /latest/configuration/container-network-interface/unmanaged-cni-providers/network-policies/
  - /docs/configuration/container-network-interface/unmanaged-cni-providers/network-policies/
title: Network policies
weight: 5
---

To circumvent system instability or even unavailability, While enabling network
policies on your cluster, make sure to always exempt the following namespaces
required by MKE 4k system components:

- `k0rdent`
- `k0s-system`
- `kube-system`
- `mke`
- `projectsveltos`
- `mgmt`
- `default`
