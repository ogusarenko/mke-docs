---
title: Verify CNI plugin installation
weight: 7
---

Upon successful installation or upgrade of the CNI plugin, all MKE 4k
components will have a `Running` status.

To review the status of the Kubernetes components:

```
/usr/local/k0s/kc get pods -n kube-system -o wide
```

<!-- Need example output as characters and not as a screenshot.>