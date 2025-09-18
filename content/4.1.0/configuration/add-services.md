---
title: Add services
weight: 3
---

{{< callout type="info" >}}

**[k0rdent](https://k0rdent.io) is a Mirantis-initiated open source project that enables lifecycle management for services on MKE 4k clusters.**

{{< /callout >}}

Through the use of the `services` section of the `mke4.yaml` configuration
file, you can add additional components to your MKE 4k installation. This section will pass the input values through to k0rdent, following the
typical [k0rdent services

Example:

```yaml
services:
   - template: project-ingress-nginx-4.11.0
     name: ingress-nginx
     namespace: ingress
```

{{< callout type="important" >}}
You must manually create all of the service templates before you apply the
configuration. To confirm that service templates exist in the MKE 4k cluster and
that they are valid for all added services:
```
kubectl get servicetemplates -A
```

{{< /callout >}}

To learn more about the services sanctioned by k0rdent and to find
instructions on how to create service templates, refer to the [k0rdent
