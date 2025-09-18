---
aliases:
  - /latest/configuration/container-network-interface/cni-configuration-example/
  - /docs/configuration/container-network-interface/cni-configuration-example/
title: CNI Configuration Example
weight: 1
---

The `network` section of the `mke4.yaml` configuration file renders as follows:

```yaml
network:
    cplb:
      disabled: true
    kubeProxy:
      iptables:
        minSyncPeriod: 0s
        syncPeriod: 0s
      ipvs:
        minSyncPeriod: 0s
        syncPeriod: 0s
        tcpFinTimeout: 0s
        tcpTimeout: 0s
        udpTimeout: 0s
      metricsBindAddress: 0.0.0.0:10249
      mode: iptables
      nftables:
        minSyncPeriod: 0s
        syncPeriod: 0s
    multus:
      enabled: false
    nllb:
      disabled: true
    nodePortRange: 32768-35535
    providers:
    - enabled: true
      extraConfig:
        cidrV4: 192.168.0.0/16
        linuxDataplane: Iptables
        loglevel: Info
      provider: calico
    - enabled: false
      provider: custom
    - enabled: false
      extraConfig:
        cidrV4: 192.168.0.0/16
        v: "5"
      provider: kuberouter
    serviceCIDR: 10.96.0.0/16
```

