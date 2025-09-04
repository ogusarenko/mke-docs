---
title: Prerequisites for unmanaged CNI on MKE 4k
weight: 2
---

Before deploying MKE 4k with an unmanaged CNI, verify that your environment
meets the prerequisites detailed herein:

| Prerequisite                    | Description                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Capable hardware | The underlying hardware for the cluster must meet the minimum deployment requirements.                                                                                                                                                                                                                                                       |
| Supported CNI Plugin            | The chosen unmanaged CNI plugin must be compatible with Kubernetes and MKE 4k. To verify this, check that the CNI has passed Kubernetes networking conformance tests.                                                                                                                                                               |
| CSI/CCM Drivers (if applicable) | If your CNI solution relies on Persistent Volumes (PVs) or Persistent Volume Claims (PVCs), a  functional Container Storage Interface (CSI) driver must be available and configured. Similarly,  if the CNI requires specific cloud provider resources, it may require installation of CCM.  For this information, refer to the CNI documentation. |
| Adequate resources             | The MKE 4k cluster must have sufficient resources (CPU, memory, storage) to run the chosen unmanaged CNI. Take into account the resource requirements of both the MKE 4k components and the chosen unmanaged CNI.                                                                                                                                                         |
| Network configuration           | You must have an adequate network configuration in place to support the unmanaged CNI. Key factors include IP addressing, routing, and firewall rules.CNI.                                                                                                                                                                                                             |

