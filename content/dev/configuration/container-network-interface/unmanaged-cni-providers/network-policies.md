---
title: Network policies
weight: 5
---

To circumvent system instability or even unavailability, While enabling network
policies on your cluster, make sure to always exempt the following namespaces
required by MKE 4k system components:

- ``k0rdent``
- ``k0s-system``
- ``kube-system``
- ``mke``
- ``projectsveltos``
- ``mgmt``
- ``default``
