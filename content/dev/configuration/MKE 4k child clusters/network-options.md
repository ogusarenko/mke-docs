---
title: Network options
weight: 5
---

Common configuration file section: `.spec.network`

- For child clusters, it is not possible to change the node port range. The
  `mke4.yaml` configuration file offers the `.spec.network.nodePortRange`
  field with `32768-35535` as the default value. No such field is available
  on a child cluster, however, and the node port range is instead set to the
  default Kubernetes value of `30000-32767`.

- The `.spec.network.providers` field in the `mke4.yaml` configuration
  file lists CNI providers, though only one of those providers can be enabled
  at a time. This is simplified for child clusters, with the field changed to
  `spec.network.provider` pointing to a single CNI provider.

Refer to [Container Network
Interfaces](../../../configuration/container-network-interface/) for more network
details.
