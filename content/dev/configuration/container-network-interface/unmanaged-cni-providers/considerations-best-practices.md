---
title: Considerations and Best Practices
weight: 4
---

Unlike the managed CNIs that MKE 4k makes available and supports, the customer
owns the entire lifecycle of whichever unmanaged CNI they choose to deploy,
from bootstrapping to periodic upgrades. To ensure the success of initial
bootstrapping of the CNI during MKE 4k installation, consider the following:

- MKE 4k uses the same default CIDR [192.168.0.0/16]for both managed and
  unmanaged CNIs. Unless you explicitly specify an IPv4 CIDR in configuration
  for the custom CNI, MKE 4k will use this CNI for installations. The existing
  cluster CIDR choice for any MKE 3 cluster that you are migrating to MKE 4k
  will continue to be honored, even if it is different from the MKE default.

- Ensure that you have budgeted for the increased resource requirements that
  unmanaged CNIs may require. You can do this by pulling resource limits and
  requests from the yaml or using a plugin such as
  https://github.com/robscott/kube-capacity.

- IPv6 support for MKE 4k is currently not available and IPv6 enabled CNIs
  cannot be used to obtain dual stack functionality.

- Before installing the unmanaged CNI, ensure the availability of any
  prerequisites by installing those first. For example, if the unmanaged cni-s
  requires CSI drivers, you must install them prior to installing the unmanaged
  CNI. Be aware, though, that in some cases running the prerequisites may
  require you to use host networking.

- Once cluster networking is established, the MKE 4k installation or upgrade
  process will automatically detect it and move on to the next stages.

- Ensure that your networking infrastructure is configured appropriately for
  the unmanaged-chi and that your cluster and service CIDR-s are disjoint along
  with the CIDR-s that are used for the private node addresses.

- Always perform extensive testing of your chosen unmanaged CNI solution in a
  non-production environment prior to production deployment.

- Implement robust monitoring for both MKE 4k and your unmanaged CNI, to
  quickly identify and resolve any networking issues.

- If you are using security modules such as Selinux or AppArmor, ensure
  proper configuration to avoid conflicts with the unmanaged CNI.

- To prevent performance bottlenecks, continuously monitor the resource use of
  your cluster and CNI components.

- The underlying Kubernetes provider used by MKE 4k [k0s] is available to use
  even without cluster networking being established. You can directly access it
  on any of the manager nodes through its binary, which is present in
  ``/usr/local/bin/k0s``. Using it is the key to installing the unmanaged CNI
  and its pre-requisite Kubernetes resources.

- You can prepare a KUBECONFIG with cluster-admin level access using
  ``/usr/local/bin/k0s`` kubeconfig admin, pipe it to a file, and use it as
  KUBECONFIG path for using helm or for using kubectl or client-go for the
  unmanaged CNI install steps.