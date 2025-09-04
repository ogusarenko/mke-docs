---
title: Unmanaged CNI Providers
weight: 6
---

Unmanaged CNI refers to the scenario wherein the Container Network Interface
solution is not directly managed by MKE 4k. Such a scenario gives users the
flexibility to integrate custom or third-party CNI plugins that are best suited
for their particular network architecture and requirements. This differs from a
managed CNI, for which MKE 4k provides and manages a default CNI solution.

{{< callout type="info" >}}

Mirantis is not responsible for the function and support of third-party
unmanaged CNIs. We provide only limited basic instruction for how to configure
unmanaged CNIs for use with MKE 4k. Thus, for detailed information on whichever
unmanaged CNI plugins you intend to deploy, you must refer to the product
documentation or support service.

{{< /callout >}}

The lifecycle for unmanaged CNIs is owned by the customers. Except for the
assumption that an unmanaged CNI is Kubernetes-compliant, MKE places no
restrictions on the mode of installation or the specifics of the unmanaged
CNIs.

Reasons to consider using an unmanaged CNI with MKE 4k include:

* To meet custom network requirements.

  Organizations with complex network setups or specific security policies or
  specific networking features may require a CNI that offers advanced features
  not available in a default managed solution.

* Legacy with with existing Infrastructure

  If you are migrating from an existing Kubernetes deployment with a specific
  CNI already in place, it may be preferable to continue using that CNI to
  minimize changes and leverage existing expertise.

* Performance Optimization

  As certain CNIs are optimized for specific workloads or network environments,
  you may prefer them for performance considerations.

* Vendor Lock-in Avoidance

  An unmanaged CNI can help keep you independent of the MKE 4k platform.

{{< callout type=info >}}

Refer to [Upgrade with an unmanaged
CNI](../../../upgrade-from-MKE-3x/upgrade-unmanaged-cni) for information
related to upgrading an MKE 3.x cluster that is running an unmanaged CNI to MKE
4k.

{{< /callout >}}