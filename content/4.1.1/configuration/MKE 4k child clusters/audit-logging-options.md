---
aliases:
  - /latest/configuration/MKE 4k child clusters/audit-logging-options/
  - /docs/configuration/MKE 4k child clusters/audit-logging-options/
title: Audit logging options
weight: 6
---

Common configuration file section: `spec.apiServer.audit`

- The `policyFile` field is not available for child clusters, as it is not
  possible to specify a custom path for an audit policy file.

- Use the `webhookConfigSecret` parameter instead of `webhookConfigPath`.
  To use the audit webhook configuration, you must create a secret in the
  `k0rdent` namespace where the `MkeChildConfig` object will be applied with the key `value` and content of the webhook configuration
  as the value, and then set the name of the secret to `webhookConfigSecret`.

Refer to [Audit logging](../../../configuration/kubernetes/audit-logging/) for more details.
