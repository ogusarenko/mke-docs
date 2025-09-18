---
title: Backup and restore
weight: 1
---

MKE 4k supports backup and restoration of cluster data through the use of the
[Velero](https://velero.io/) add-on. System backup is enabled by default.

## Backup configuration

The `backup` section of the `mke4.yaml` configuration file renders as follows:

```yaml
backup:
  enabled: true
  storage_provider:
    type: InCluster
    in_cluster_options:
      distributed: false
```

By default, MKE 4k supports backups that use the in-cluster storage
provider, as shown in the `type.InCluster` field.
In-cluster backups for MKE 4k are implemented using the
[MinIO add-on](https://min.io/).

The `distributed` setting configures MinIO storage to run in distributed mode.

Refer to the following list for detail on all the configuration file `backup` fields:

<!-- [TODO turn this list into a table once column widths are fixed] -->

`enabled` 
: Indicates whether backup/restore functionality is enabled.

  - Valid values: `true`, `false`
  - Default: `true`

`storage_provider.type `

: Indicates whether the storage type in use is in-cluster or external.

  -  `InCluster`, `External`
  - Default: `InCluster`

`storage_provider.in_cluster_options.distributed`

: Indicates whether to run MinIO in distributed mode.

  - Valid values: `true`, `false`
  - Default: `false`

`storage_provider.external_options.provider`

: Name of the external storage provider. Currently, AWS is the only available option.

  - Valid values: `aws`
  - Default: `aws`

`storage_provider.external_options.bucket`

: Name of the pre-created bucket to use for backup storage.

`storage_provider.external_options.region `

: Region in which the bucket exists.

`storage_provider.external_options.credentials_file_path`

: Path to the Credentials file.

`storage_provider.external_options.credentials_file_profile`

: Profile in the Credentials file to use

## Create a backup and perform a restore

For information on how to create backups and perform restores for both storage
provider types, refer to:

- [External storage provider](external)
- [In-cluster storage provider](in-cluster)

## Existing limitations

- MKE 4k does not currently support:

  - scheduled backups
  - backup to NFS storage
  - backup to local disks

- Restoration Scope: Backups can only be restored to the same cluster where they
  were originally created. Restoring to a new set of nodes is not supported.

