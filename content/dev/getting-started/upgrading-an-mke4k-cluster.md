---
title: Upgrading an existing MKE 4k cluster
weight: 8
---

The current upgrade paths for MKE 4k clusters are detailed below:

| Source version 	| Target version 	| mkectl version required 	|
|---	|---	|---	|
| 4.1.0 	| 4.1.1 	| 4.1.1 	|

{{< callout type=info >}}

As MKE 4.1.0 is built on an alpha version of k0rdent, upgrading from
that MKE 4k version involves a complete reinstallation of MKE 4k and
k0rdent, resulting in a more prolonged upgrade process.

{{< /callout >}}

**To upgrade your MKE 4k cluster:**

1. Verify that you have the latest version of the mkectl binary. For
   information on how to download and install mkectl, refer to [Install the MKE
   4k CLI](../../getting-started/install-MKE-4k-CLI).

2. [Back up etcd](backup-etcd) prior to initiating the upgrade.

   {{< callout type=info >}}

   If you do not provide a path to an etcd backup as part of the
   ``mkectl upgrade`` command, mkectl automatically generates a pre-upgrade
   backup prior to initiating the upgrade.

   {{< /callout >}}

3. Run the upgrade.

   ```bash
   mkectl upgrade --upgrade-version <version_to_upgrade_to> --etcd-snapshot-path
   <path_to_pre-upgrade_snapshot>
   ```
