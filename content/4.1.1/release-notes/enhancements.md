---
aliases:
  - /latest/release-notes/enhancements/
  - /docs/release-notes/enhancements/
title: Enhancements
weight: 2
---

Detail on the enhancements introduced in MKE 4k 4.1.1 includes:

- **MKE 4k child clusters with Mirantis k0rdent Enterprise 1.1.0**

  MKE 4k 4.1.1 includes Mirantis k0rdent Enterprise 1.1.0 by default, thus
  enabling the seamless deployment of MKE 4k child clusters across multiple
  providers from an MKE 4k mothership cluster. Users can start with the default
  cluster template, or work with a Mirantis representative to create a custom
  cluster template to meet your specific needs.

  To ensure consistency, the mothership cluster monitors and corrects
  configuration drift for MKE 4k services and resources. New clusters
  automatically adopt a standardized default configuration, which simplifies
  multi-cluster deployments across clouds or on-prem environments.

  Refer to [MKE 4k child clusters](../../concepts/child-clusters) and
  [Configure child clusters](../../configuration/MKE-4k-child-clusters) for more information.
<br><br>
- **Custom CA certificates for air-gapped environments**

  With the 4.1.1 release, MKE 4k now supports custom registries with TLS
  certificates from private, self-signed Certificate Authorities (CAs). This is
  critical for air-gapped environments, as it enables secure image management
  without internet access, which is ideal for customer segments that must run
  the product in isolation, such as finance or government institutions.

  Refer to [TLS certificates](../../configuration/tlscertificates/) for more information.
<br><br>
- **Improved upgrades, including dry run functionality**

  Upgrading to 4.1.1 from MKE 3.7, MKE 3.8, and MKE 4k 4.1.0 is now a smoother
  process. In particular, it brings key MKE 3 features forward, and thus closes
  the adoption gap for MKE 3 users, offering minimal disruption and enhanced
  stability. In addition, with the new dry-run feature you can simulate
  upgrades, validate configurations, and identify potential issues before you
  upgrade, to ensure that you production clusters remain reliable.

  Refer to [Perform the Upgrade](../../upgrade-from-MKE-3x/perform-migration)
  and [Upgrade Compatibility
  checks](../../upgrade-from-MKE-3x/compatibility-checks) for more information.
<br><br>
- **Custom CNI enablement, including Calico Enterprise**

  The 4.1.1 release comes complete with Calico OSS CNI for robust networking,
  and it now enables custom CNIs such as Calico Enterprise. The flexibility
  provided by this function allows you to leverage advanced network policies
  and observability, tailoring Kubernetes networking to your needs while also
  maintaining CNCF-certified reliability.

  Refer to [Configure CNI Providers](../../configuration/container-network-interface/configure-cni-providers/#calico-oss) for more information.

<!--- [BOP-2260] -->

- **MKE 4k now only recognizes the `controller+worker` and `worker` role
  types**

## Backend enhancements

- Support registeries with private CAs and backend redirects.
- etcd storage quota checks.
- Allow configuration of mke4k data-dir.
- Registry support for child clusters.
- Publish sha256 checksum of an offline bundle.
- Use of tigera operator CRD chart during upgrade.
- Addition of `check` command for upgrades.
- Introduction and full user control of ucpauthz VAP.
- Addition of an upgrade summary to output files.
- Airgap MKE 4k to MKE 4k upgrade capability.
- Ability to upgrade from an MKE 3 system with cert-manager.
- Addition of `--dry-run` and `--output ` flags, to obtain the `mke3.toml`
  configuration file and `mke4.yaml` configuration file.
- Addition of automated support dump collection, to diagnose upgrade failures.
- Addition of configurable time windows for installation and upgrade tasks,
  such as the installation of custom CNIs.
- Rollback capability during MKE 4k to MKE 4k upgrade.
- Addition of a k0rdent private CA for registry.
- Support of MKE 3 upgrades from clusters that use an unmanaged CNI.
- Reinstallation of k0rdent during upgrade.
- Support for configuring audit policy and change default to Metadata.
- Allow for configuration of Dex `replicaCount` and `topologySpreadConstraints`.
- Introduction of a configurable kubelet root directory, to ensure the function
  of kubelet device plugins.
- Addition of configurable OIDC audiences and default kubelogin audience.
- Support for mkectl `services` command.
- Private registry CA for containerd.
- Addition of the ucpathz extension to child clusters.
- Addition of validation for specified credentials in `MkeChildConfig` object.
- Prevention of user service overrides of system services.
- Addition of read-only UI in child clusters.
- Allow non-default k0s data directory.
- Setting of `k0rdent.enabled` parameter to `true` for a new cluster.
- Support for custom TLS certificates.
- Extension of `MkeChildConfig` object reconciliation logic.
- Addition of a configuration parameter you can use to disable local users.
- Addition of MKE child cluster API.
- Addition of audit policy support.

## UI enhancements

- MKE 4k Dashboard for child clusters operates in read-only mode.
- Addition of etcd corrupt banner.
- Addition of CPU usage banner.
- Addition of etcd quota banner.
- Use of natural sort order over lexographical order in lists.