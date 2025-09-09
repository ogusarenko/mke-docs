---
title: Audit logging
weight: 4
---

Kubernetes provides a powerful and extensible API that serves as the central
control point for managing clusters. Every action flows through the Kubernetes
API server, whether it is the creation of a Pod, the modification of a
deployment, or secrets retrieval. This design makes the API server the
authoritative source of truth; however, it also issues a critical challenge:
visibility into who did what, when, and from where.

Audit logging addresses the visibility challenge. By enabling audit logs on the
API server, administrators can gain a chronological record of all requests that
are received by the cluster. The log entries capture such essential details as
the requesting user or service account, the action performed, the target
resource, and whether the action was permitted or denied. Thus, audit logging
provides a comprehensive trail of activity that can be analyzed for operational
insights, compliance, and security.

## Enable Audit logging

You can enable Kubernetes API server audit logging through the MKE
configuration file, either during or after MKE 4k installation. The function is
controlled by the ``spec.apiServer.audit`` section of the MKE 4k configuration
file, and to enable it you set the  ``spec.apiServer.audit.enabled``
parameter to ``true``.

Example MKE 4k configuration:

``` yaml
spec:
  apiServer:
    audit:
      enabled: false
      logPath: /var/lib/k0s/audit.log
      maxAge: 30
      maxBackup: 10
      maxSize: 10
```

## Configuration fields

| Field 	| Description 	|
|---	|---	|
| Enabled 	| Enables or disables API server audit logging.<br><br>  Default: ``false`` 	|
| LogPath 	| Filesystem path where audit logs are written.<br><br>   Default: ``/var/log/k0s/audit/mke4_audit.log`` {{< callout type=“warning" >}} MKE 4k strictly enforces the default value, and will reject any attempt to configure a custom log path. {{< /callout >}}	|
| Level 	| Specifies the audit logging level.<br><br>  Valid values: ``none``, ``metadata``, ``request``.<br><br>  ``metadata`` 	|
| MaxAge 	| Maximum number of days to retain old audit log files.<br><br>  Default: ``30`` 	|
| MaxBackup 	| Maximum number of old audit log files to retain.<br><br>  Default: ``10`` 	|
| MaxSize 	| Maximum size (in MB) of the audit log file before rotation.<br><br>  Default: ``10`` 	|
| CustomPolicyYaml 	| Inline YAML definition of a custom audit policy. If set, overrides the default policy.<br><br>  Default: ``“”`` 	|
| PolicyFile 	| Filesystem path to the audit policy file used by the API server.<br><br>  Default: ``/var/lib/k0s/mke4_audit_policy.yaml`` {{< callout type=“note" >}}The default file path cannot be changed. You can still provide a custom audit policy using  ``spec.apiServer.audit.customPolicyYaml``, however the ``policyFile`` path itself is immutable. {{< /callout >}}	|
| WebhookConfigPath 	| Path to the file containing the webhook configuration backend for sending audit events.<br><br>  Default: ``“”`` 	|

## Comparison to MKE 3x Audit Logging

MKE 4k improves on MKE 3x Kubernetes API server audit logging, providing better
visibility, flexibility, and control over cluster activity. Key enhancements
include:

-  ``Metadata`` as the default audit level

   In MKE 4k, the API server defaults to level ``Metadata``, in alignment with
   the recommended minimal audit policy that is recommended by Kubernetes. In
   comparison, the default level for MKE 3x is from MKE-3 is ``None``.

- Configurable audit levels

  MKE 4k users can configure the audit log level through the MKE 4k
  configuration file, under ``apiServer.audit.level``. The values that are
  supported are ``None``, ``Metadata``, and ``Request``.

  To match the audit policy tuning available in MKE 3x, MKE 4k uses a specific
  audit stage ``RequestReceived``, for noise reduction in the audit logs.

- Support for Custom Audit Policies

  MKE 4k adds support for fully customizable audit policies by way of the
  ``customPolicyYaml`` field in the API server configuration:

  ``` yaml
  apiServer:
    externalAddress: <load-balancer>
    audit:
      enabled: true
      level: metadata
      logPath: /var/log/k0s/audit/mke4_audit.log
      customPolicyYaml: |
        apiVersion: audit.k8s.io/v1
        kind: Policy
        rules:
        - level: Request
  ```

  When a custom policy is defined, MKE 4k ignores the default audit policy and
  instead applies the configuration provided by the user.
