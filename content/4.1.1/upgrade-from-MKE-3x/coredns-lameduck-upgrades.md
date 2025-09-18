---
aliases:
  - /latest/upgrade-from-MKE-3x/coredns-lameduck-upgrades/
  - /docs/upgrade-from-MKE-3x/coredns-lameduck-upgrades/
title: CoreDNS Lameduck Upgrades
weight: 8
---

MKE 4k supports upgrading from MKE 3 systems that are running with CoreDNS and
Lameduck enabled. Refer to the table below for a comparison of the CoreDNS
Lameduck configuration parameters between MKE 3 and MKE 4k:

| MKE 3                                              | MKE 4k                 |
| -------------------------------------------------- | --------------------- |
| [cluster_config.core_dns_lameduck_config.enabled]  | dns.lameduck.enabled  |
| [cluster_config.core_dns_lameduck_config.duration] | dns.lameduck.duration |

