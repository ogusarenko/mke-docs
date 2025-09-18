---
aliases:
  - /latest/release-notes/addressed-issues/
  - /docs/release-notes/addressed-issues/
title: Addressed issues
weight: 3
---

Issues addressed in the MKE 4k 4.1.1 release include:

- Sensitive information is now masked in debug logs.
- Disabling of user management link when local users is disabled.
- Handling of LDAP SSL invalid configuration during upgrade.
- Removal of redundant and unnecessary debug/info logs.
- Per-node flags precedence is now respected.
- Fixed an issue wherein during migration an RBAC grant applied to a team was
  not correctly migrated.
- Ensure debug level k0s logs.
- Fixes to broken webhooks and addition of validations for audit fields.
- Hiding of LDAP Bind PW from the authentication configuration.
- Removal of ports from Ingress hosts.
- Removal of the client secret from the MKE 4k Dashboard.
- Fixed an issue wherein kubelet parameters were immutable following the
  initial application.