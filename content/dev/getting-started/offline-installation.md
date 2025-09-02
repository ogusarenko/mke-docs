---
title: Offline installation
weight: 3
---

The installation and upgrade procedures for MKE 4k reflect those of the online
scenario. While the online installation typically uses
`registry.mirantis.com` as the primary OCI registry for MKE
 4k materials, with the offline scenario you instead specify a private
registry from which to pull the MKE 4k images and charts.

## Dependencies ##

- [skopeo](https://github.com/containers/skopeo) 1.6.1 or later
- An OCI-based private registry that is accessible from all cluster nodes.
  - All MKE 4k images and charts must be publicly accessible, with no required authentication.
  - The registry must use HTTPS.
  - The registry must support multi-level nesting. For example,
    `registry.com/level-one/level-two/level-three/image-name:latest`. Some
    registries only allow one level of nesting, such as
    `registry.com/level-one/image:latest`, so verify that your registry
    supports deeper nesting for image names.

## Preparation ##

1. Download the offline bundle from the command line as follows:

     ```bash
     MKE_VERSION="4.1.0"
     DOWNLOAD_URL="https://packages.mirantis.com/caas/mke_bundle_v${MKE_VERSION}.tar.gz"
     BUNDLE_NAME="mke_bundle_v${MKE_VERSION}.tar.gz"

     curl -L "$DOWNLOAD_URL" -o "$BUNDLE_NAME"
     ```

2. Transfer the bundle file to a machine that can access your private registry.

3. On the machine with registry access, set the environment variables:

   ```bash
   MKE_VERSION="4.1.0"
   export REGISTRY_ADDRESS='<registry_address>'
   export REGISTRY_PROJECT_PATH='<registry-path>'
   export REGISTRY_USERNAME='<username>'
   export REGISTRY_PASSWORD='<password>'
   export BUNDLE_NAME="mke_bundle_v${MKE_VERSION}.tar.gz"
   ```

| Environment variable                             | Description                                                                                                                                                                                                                                                                                 |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REGISTRY_ADDRESS       | Registry hostname (required) and port (optional). The value must not end with a slash '/'.<br><br>Example: `private-registry.example.com:8080`                                                                                                                                              |
| REGISTRY_PROJECT_PATH        | Path to the registry project that will store all MKE 4k artifacts. The registry address and path should comprise the full registry path. The value must not end with a slash '/'.<br><br>Example: `REGISTRY_ADDRESS + '/' + REGISTRY_PROJECT_PATH == 'private-registry.example.com:8080/mke` |
| REGISTRY_USERNAME                | Username for the account that is allowed to push.                                                                                                                                                                                                                                           |
| REGISTRY_PASSWORD        | Password for the account that is allowed to push.                                                                                                                                                                                                                                           |
| BUNDLE_NAME| The name of previously downloaded bundle file, which must be located in the same directory in which you run the preparation steps.                                                                                                                                                          |

4. Upload the MKE 4k images and helm charts to your private registry:

   ```bash
   # Login to the registry
   skopeo login "$REGISTRY_ADDRESS" -u "$REGISTRY_USERNAME" -p "$REGISTRY_PASSWORD"

   # Extract the bundle
   tar -xzf "$BUNDLE_NAME" -C ./

   # Iterate over bundle artifacts and upload each one using skopeo
   for archive in $(find ./bundle -print | grep ".tar"); do
     # Form the image name from the archive name
     img=$(basename "$archive" | sed 's~\.tar~~' | tr '&' '/' | tr '@' ':');

     echo "Uploading $img";
     # Copy artifact from local oci archive to the registry
     skopeo copy -q --retry-times 3 --multi-arch all "oci-archive:$archive" "docker://$REGISTRY_ADDRESS/$REGISTRY_PROJECT_PATH/$img";
   done;
   ```

## Installation ##

{{< callout type="info" >}}

For information on performing an upgrade to an existing MKE 3 installation in an
airgap environment, refer to [Offline
upgrade](../../upgrade-from-mke-3x/perform-migration#offline-upgrade).

{{< /callout >}}

1. Refer to the [Create a Cluster](../create-cluster/#initialize-deployment) procedure for detail on
how to create an `mke4.yaml` configuration file.

2. Add the following additional settings to the `mke4.yaml` configuration file:

   | Setting                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
   |-----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `.spec.registries.imageRegistry.URL`    | Sets your registry address with a project path that contains your MKE 4k images. For example, `private-registry.example.com:8080/mke`. <br><br>The setting must not end with a slash `/`.<br><br>The port is optional.                                                                                                                                                                                                                                                           |
   | `.spec.registries.chartRegistry.URL`    | Sets your registry address with a project path that contains your MKE 4k helm charts in OCI format. For example, `oci://private-registry.example.com:8080/mke`.<br><br>The setting must always start with `oci://`, and it must not end with a slash `/` .<br><br>If you uploaded the bundle as previously described, the registry address and path will be the same for chart and image registry, with the only difference being the `oci://` prefix in the chart registry URL. |
   | `.spec.registries.imageRegistry.caData` | 	PEM encoded certificate of the Certificate Authority that issued image registry TLS certificates. Optional. Must be provided if registry is using TLS certs issued by a non-publicly trusted CA.                                                                                                                                                                                                                                                                                |
   | `.spec.registries.chartRegistry.caData` | 	PEM encoded certificate of the Certificate Authority that issued chart registry TLS certificates. Optional. Must be provided if registry is using TLS certs issued by a non-publicly trusted CA.                                                                                                                                                                                                                                                                                |
   | `.spec.airgap.enabled = true`           | Indicates that your environment is airgapped.                                                                                                                                                                                                                                                                                                                                                                                                                                    |

   Example:

   ```
   spec:
     airgap:
       enabled: true
     registries:
       chartRegistry:
         url: oci://private-registry.example.com:8080/mke
         caData: |-
           -----BEGIN CERTIFICATE-----
           MIIFejCCA2KgAwIBAgIUGUBk1RMNa2X/dijEzY0OwxnuFHowDQYJKoZIhvcNAQEL
           ...
           2+yWdp5K3LBV/+OtCEA=
           -----END CERTIFICATE-----
       imageRegistry:
         url: private-registry.example.com:8080/mke
         caData: |-
           -----BEGIN CERTIFICATE-----
           MIIFejCCA2KgAwIBAgIUGUBk1RMNa2X/dijEzY0OwxnuFHowDQYJKoZIhvcNAQEL
           ...
           2+yWdp5K3LBV/+OtCEA=
           -----END CERTIFICATE-----
   ```

3. Run the `mkectl apply` command.

## MKE 4k versus MKE 3 ##

MKE 3 requires the use of the `docker load` command to load offline bundles
directly into Docker on every cluster node. While this approach does not
require you to have a private registry, it also means that the cluster cannot
re-pull the image should any of the loaded images go missing. As such, MKE 3
users must disable Kubernetes garbage collection, which can sometimes prune
images of optional components that are not always enabled. This is not an issue
with MKE 4, as images are pulled from a private registry that the customer
provides, and thus there is no need to disable Kubernetes garbage collection.
That said, though, users must ensure that the registry is available at all
times and that it is accessible from every cluster node.
