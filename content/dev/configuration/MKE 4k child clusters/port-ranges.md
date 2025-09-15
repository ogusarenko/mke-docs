---
title: Port ranges
weight: 15
---

Standalone MKE clusters have a custom Kubernetes node port range of
`32768-35535`, as well as having ports `33000` and `33001` reserved for
Ingress controller http and https, respectively. Due to CAPI limitations, for
child clusters the Kubernetes node port range is `30000-32767`, and it cannot
be changed. In correlation, the node ports for the Ingress controller changed
to `30000` and `30001`, respectively.

Refer to the following affected example configurations:

- [CNI configuration example](../../container-network-interface/cni-configuration-example/)
- [Ingress Controller configuration example](../../../configuration/ingress/#example-ingress-controller-configuration)