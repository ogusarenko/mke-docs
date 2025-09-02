---
title: TLS certificates
weight: 10
---

To ensure all communications between clients and MKE 4k are encrypted, MKE 4k
services are exposed using HTTPS. By default, this is done using self-signed
TLS certificates that are not trusted by client tools such as web browsers.
Thus, when you try to access MKE 4k, your browser warns that it does not trust
MKE 4k or that MKE 4k has an invalid certificate.

You can configure MKE 4k to use your own TLS certificates. As a result, your
browser and other client tools will trust your MKE 4k installation.

Mirantis recommends that you make TLS certificate changes outside of peak
business hours. Your applications will continue to run normally. However, the
Ingress Controller will restart, and applications exposed through it may
experience a short period of unavailability.

Use the MKE 4k CLI to configure MKE 4k to use your own TLS certificates and
keys:

1. All keys and certificates must be uploaded in PEM format, and the
   certificates must include the external address from
   ``.spec.apiServer.externalAddress`` in the SANs list.

2. In the ``mke4k.yaml`` configuration file, enable your custom TLS
   certificates:

   1.  Set ``.spec.certificates.enabled`` to ``true``.

   2. Add your TLS certificates in the PEM format under ``ca``, ``cert`` and
      ``key``, as illustrated below:

      ```yaml
       spec:
        certificates:
          enabled: true
          ca: |-
            -----BEGIN CERTIFICATE-----
            <pem-data>
            -----END CERTIFICATE-----
          cert: |-
            -----BEGIN CERTIFICATE-----
            <pem-data>
            -----END CERTIFICATE-----
          key: |-
            -----BEGIN PRIVATE KEY-----
            <pem-data>
            -----END PRIVATE KEY-----
      ```

3. Apply the configuration:

   ```bash
   mkectl apply
   ```

4. Once the ``mkectl apply`` command completes, to avoid storing sensitive data
   in the local file you can remove the ``cert`` and ``key`` fields from
   ``.spec.certificates``, as at this point the certificates have been stored
   securely in the cluster.

   {{< callout type=“warning >}}
   Do not remove the ``ca`` key and its value from ``.spec.certificates``.
   {{< /callout >}}