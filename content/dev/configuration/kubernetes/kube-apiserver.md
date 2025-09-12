---
title: kube-apiserver
weight: 3
---

The Kubernetes API server validates and configures data for the API objects,
which include pods, services, and replication controllers, among others. The
server performs REST operations while also serving as the frontend to the
shared state of a cluster, through which the other components interact.

## API server configuration

You can configure the Kubernetes API server for all controllers through the
`apiServer` section of the `mke4.yaml` configuration file, an example of which
follows:

```yaml
spec:
  apiServer:
    audit:
      enabled: false
      logPath: /var/lib/k0s/audit.log
      maxAge: 30
      maxBackup: 10
      maxSize: 10
    encryptionProvider: /var/lib/k0s/encryption.cfg
    eventRateLimit:
      enabled: false
    requestTimeout: 1m0s
```

You can further configure the Kubernetes API server using the `extraArgs` field
to define flags. This field accepts a list of key-value pairs, which are passed
directly to the kube-apiserver process at runtime.

## Encryption configuration

MKE 4k provides at-rest encryption for cluster secrets using the `aescbc`
provider with a static secret. The encryption configuration is generated during
initial cluster provisioning and is stored on every manager node at
`/var/lib/k0s/encryption.cfg`.

An example of the default `encryption.cfg` file follows:

```yaml
kind: EncryptionConfig
apiVersion: v1
resources:
  - resources:
    - secrets
    providers:
    - aescbc:
        keys:
        - name: key
          secret: <randomly_generated_key>
```

To change the configuration, to add a KMS provider, for example, you use the
default `encryption.cfg` file as the basis for creating a new encryption
configuration file.

{{< callout type=warning >}}

Backup the encryption config file before making any changes:

```bash
sudo cp /var/lib/k0s/encryption.cfg /path/to/backup/folder/encryption.cfg.backup
```

Be aware that if you lose the encryption key, all previously created secrets
will be lost and will need to be recreated.

{{< /callout >}}

1. Copy the `encryption.cfg` file and give it a new name.

   ```bash
   sudo cp /var/lib/k0s/encryption.cfg /var/lib/k0s/<new_encryption_filename>.cfg
   ```

   {{< callout type=info >}}

   Refer to official Kubernetes documentation [Encrypting Confidential Data at
   Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for
   information on supported configurations. When adding new providers, be sure
   to prepend them to the existing aescbc provider so that they take a higher
   precedence.

   {{< /callout >}}

2. Verify that the modified encryption configuration file is owned by the
   `kube-apiserver` user with `600` permissions.

   ```bash
   sudo chown kube-apiserver:root /var/lib/k0s/<new_encryption_filename>.cfg
   sudo chmod 600 /var/lib/k0s/<new_encryption_filename>.cfg
   ```

3. Place copies of the modified encryption configuration file at the same
   locations on each manager node.

4. Edit the `.spec.apiServer.encryptionProvider` parameter in the
   `mke4.yaml` configuration file to point to the new encryption
   configuration file:

   ```yaml
   spec:
     apiServer:
       encryptionProvider: /var/lib/k0s/<new_encryption_filename>.cfg
   ```

5. Apply the `mke4.yaml` configuration file to set the encryption
   configuration:

   ```bash
   mkectl apply
   ```
