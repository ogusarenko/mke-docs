---
aliases:
  - /latest/configuration/container-network-interface/network-configuration/
  - /docs/configuration/container-network-interface/network-configuration/
title: Network Configuration
weight: 2
---

The following table includes details on all of the configurable `network` fields.

| Field | Description | Values |  Default |
|-------|-------------|--------|----------|
| `serviceCIDR` | Sets the IPv4 range of IP addresses for services in a Kubernetes cluster. | Valid IPv4 CIDR | `10.96.0.0/16` |
| `providers` | Sets the provider for the active CNI. | `calico` | `calico` |
