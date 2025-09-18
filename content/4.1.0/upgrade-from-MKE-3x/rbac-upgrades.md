---
title: RBAC Upgrades
weight: 7
---

As MKE 4k does not support Swarm mode, the platform uses standard [Kubernetes
As such, the Swarm authorization configuration that is in place for MKE 3 is not present in MKE 4.

### Groups

To enable the same RBAC hierarchy as in MKE 3 with `orgs` and `teams` groups, but
without the two-level limitation, MKE 4k replaces `orgs` and `teams` with
the Kubernetes `AggregatedRoles`.

**Authorization structure comparison:**

```
MKE 3:                           MKE 4:

├── entire-company (org)         ├── entire-company-org (AggregatedRole)
│   ├── development (team)       ├── development-team (AggregatedRole)
│   │   ├── bob (user)           │   ├── bob (user)
│   ├── production (team)        ├── production-team (AggregatedRole)
│   │   ├── bob (user)│          │   ├── bob (user)
│   │   ├── bill (user)          │   ├── bill (user)
│   ├── sales (team)             ├── sales-team (AggregatedRole)
```

### Roles

Roles are bound to the aggregated roles for integration into the org, team, and user structure.
Thus, what was previously an organization or a team role will have `-org` or `-team`
appended to its name.

A role can be assigned at any level in the hierarchy, with its permissions granted to all members
at that same level.

**Example organization binding:**

```
├── entire-company-org (AggregatedRole) -- entire-company-org (RoleBinding) -- view (Role)
│   ├── development-team (AggregatedRole)
│   │   ├── bob (user)
│   ├── production-team (AggregatedRole)
│   │   ├── bob (user)
│   │   ├── bill (user)
│   ├── sales-team (AggregatedRole)
```

In the example above, all members of the `entire-company` org group have
`view` permissions. This includes the `development-team`,
`production-team`, `sales-team`, `bob`, and `bill`.

**Example team binding:**

```
│   ├── development:team (AggregatedRole) -- development:team (RoleBinding) -- edit (Role)
│   │   ├── bob (user)
```

In the example above, the binding grants `edit` permissions only to the
members of the development team, which only includes `bob`.

{{< callout type="warning" >}}

Swarm roles are partially translated to Kubernetes roles. During upgrade,
any detected Swarm role is replicated without permissions, thus
preserving the org/team/user structure.
If no Swarm roles are detected, a `none` role is created as a placeholder,
as Kubernetes requires each aggregated role to have at least one role.
This `none` role has no permissions, with its only purpose being to maintain
structural integrity.

{{< /callout >}}
