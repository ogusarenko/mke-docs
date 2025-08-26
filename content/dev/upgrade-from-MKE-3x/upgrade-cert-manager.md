---
title: Upgrade with cert-manager
weight: 9
---

The [cert-manager](https://cert-manager.io/) instance built into MKE 4k
will conflict with any cert-manager instances brought in as part of an MKE 3
cluster upgrade. Thus, it is necessary to uninstall any such cert-manager
instances prior to performing the upgrade. You can, though, retain your
cert-manager CRDs and cert-manager resources, such as certificates and issuers.

{{< callout type="warning" >}}

Ensure that all of the certificates managed by the cert-manager will remain
valid throughout the duration of your upgrade to MKE 4k.

Mirantis recommends that you force-renew any certificates that are close to
expiration to minimize the risk of errors when the cert-manager is unavailable
during the upgrade.

{{< /callout >}}
<br>

<details>

<summary><b>To uninstall a manifest-based cert-manager</b></summary>

If cert-manager was installed on your MKE 3 cluster using the `kubectl apply`
command with a cert-manager manifest, you can run the `kubectl delete`
command to uninstall cert-manager components.

To retain your cert-manager CRDs and custom resources so they can be
automatically picked up by the MKE 4k cert-manager once the upgrade has
completed, modify the `cert-manager-provided` manifest prior to running the
`kubectl delete` command:

1. Download the cert-manager manifest for the version of cert-manager you are
   running on your MKE 3 cluster:

   ```
   curl -L https://github.com/cert-manager/cert-manager/releases/download/vX.Y.Z/cert-manager.yaml -o cert-manager.yaml
   ```

2.  Verify that  [`yq`](https://github.com/mikefarah/yq) is installed on your system.

3. Delete all CRDs from the manifests:

   ```
   yq 'select(.kind != "CustomResourceDefinition")' cert-manager.yaml > cert-manager-no-crds.yaml
   ```

3. Remove all cert-manager components, except for the CRDs:

   ```
   kubectl delete -f cert-manager-no-crds.yaml
   ```

4. Proceed with the upgrade to MKE 4k.

</details>
<br>
<details>

<summary><b>To uninstall a Helm-based cert-manager with CRDs managed separately</b></summary>

The cert-manager helm chart allows for separate CRD management. For this, you must:

1. Install CRDs with the `kubectl apply` command.
2. Install cert-manager helm chart:
   - `crds.enabled: false` for version 1.15 and later
   - `installCRDs: false` for versions prior to 1.15

To verify your installation type, run:

```
helm get values cert-manager
```

If the output contains `installCRDs: false` or `crds.enabled: false`, proceed
with the uninstallation. All of your CRDs and cert-manager resources will be safely
retained, as they are not managed by Helm. If, however, the output is
`installCRDs: true` or `crds.enabled: true`, refer to the collapsible section
that follows, "Uninstall Helm-based cert-manager with CRDs managed by Helm"

To uninstall cert-manager, run:

```
helm uninstall <cert-manager-release-name> -n <cert-manager-namespace>
```

</details>
<br>
<details>

<summary><b>To uninstall a Helm-based cert-manager with CRDs managed by Helm</b></summary>

To uninstall a cert-manager that was installed using a Helm chart with the
`crds.enabled: true` value, or `installCRDs: true` for versions prior to 1.15,
first ensure that you are running version 1.15 or later. Versions prior to 1.15
do not support the retention of CRDs during the helm uninstall, and thus if
you run the command you will lose all of your CRDs and custom resources.
As such, if your version of cert-manager is earlier than 1.15, you must
upgrade it before running `helm uninstall`.

1. If necessary, upgrade cert-manager to version 1.15:

   ```
   helm upgrade --reset-then-reuse-values --version 1.15.5 --set crds.enabled=true --set crds.keep=true --set installCRDs=false cert-manager jetstack/cert-manager
   ```

   {{< callout type="info" >}}

   cert-manager recommends that you do not skip over minor versions when
   upgrading, and so it may be necessary to perform multiple upgrades to reach version 1.15.

   {{< /callout >}}

2. Verify that your cert-manager is configured to retain CRDs:

   1. Print the values of the cert-manager helm release:

      ```
      helm get values cert-manager
      ```

   2. Confirm that the output does not contain the following:

      ```
      crds:
          keep: false
      ```

      The output may contain `crds.keep: true` or nothing related to `crds.keep`,
      in which case it is `true` by default. If the output is `crds.keep: false`, set it to `true`:

      ```
      helm upgrade --reuse-values --version <current-cert-manager-version> --set crds.keep=true cert-manager jetstack/cert-manager
      ```

3. Uninstall cert-manager:

   ```
   helm uninstall <cert-manager-release-name> -n <cert-manager-namespace>
   ```

   Example output:

   ```
   These resources were kept due to the resource policy:
   [CustomResourceDefinition] certificaterequests.cert-manager.io
   [CustomResourceDefinition] certificates.cert-manager.io
   [CustomResourceDefinition] challenges.acme.cert-manager.io
   [CustomResourceDefinition] clusterissuers.cert-manager.io
   [CustomResourceDefinition] issuers.cert-manager.io
   [CustomResourceDefinition] orders.acme.cert-manager.io
   ```

4. Proceed with the upgrade to MKE 4k.

</details>

References: [cert-manager installation
documnetation](https://cert-manager.io/docs/installation/)
