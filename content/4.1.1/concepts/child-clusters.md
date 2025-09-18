---
aliases:
  - /latest/concepts/child-clusters/
  - /docs/concepts/child-clusters/
title: MKE 4k child clusters
weight: 5
---

All MKE 4k clusters come complete with k0rdent Enterprise v1.1.0 and can act as
mothership clusters. Refer to the k0rdent documentation, [Mirantis k0rdent
Enterprise Concepts](https://docs.k0rdent-enterprise.io/latest/concepts/), for
more information.

{{< callout type="warning" >}}

- If you decide to use the `mkectl reset` command to wipe the standalone MKE
  cluster that is serving as the mothership cluster, you should delete all the
  existing child clusters as otherwise they will remain in an unmanaged state.

- Currently, `MkeChildConfig` objects can only be applied to a
  `k0rdent` namespace.

{{< /callout >}}

Key child cluster requirements include:

- Centralized management and configuration.

  In MKE 4k, the mothership cluster serves as the single source of truth for
  managing child clusters. Operators do not need to switch contexts between
  mothership and child clusters when provisioning or managing workloads, as the
  mothership handles this orchestration automatically.

  To maintain consistency, the MKE dashboard in child clusters presents in
  read-only mode. This ensures that all configuration changes flow through the
  mothership, which prevents drift and maintains management cluster authority.

- Configuration in line with that of a standalone cluster.

- Exclusion of the mkectl CLI from deployment to allow for the configuration
  of GitOps systems and management of multiple child clusters at the same
  time.

To meet these requirements, the MKE 4k API offers the CRD object
`MkeChildConfig`. This object is similar to `MkeConfig`, which is used to
deploy standalone clusters, with the most significant difference being the
infrastructure section, which contains the configuration for deploying the
infrastructure for the MKE 4k cluster. With only a few exceptions, all other
sections of `MkeChildConfig` are the same as those in `MkeConfig`.

{{< callout type="info" >}}

Currently, AWS is the only supported cloud provider for MKE 4k child clusters.

{{< /callout >}}

Refer to [Deploy an MKE 4k child cluster](../../tutorials/mke4k-child-clusters/deploy-mke4k-child-cluster) for a comprehensive feature tutorial.