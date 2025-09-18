---
aliases:
  - /latest/getting-started/system-requirements/
  - /docs/getting-started/system-requirements/
title: System requirements
weight: 1
---

Before you start cluster deployment, verify that your system meets the following minimum hardware
and software requirements.

## Hardware requirements

Although MKE 4k uses [k0s](https://k0sproject.io/) as the underlying Kubernetes distribution, the hardware requirements for MKE 4k differ from k0s due to the higher resource requirements of various enterprise-grade components the software uses.

To ensure stability, optimal performance, and reliable upgrades, Mirantis performed extensive internal testing to determine the optimum hardware configuration for MKE 4k:

- vCPUs: 8 vCPUs per node
- RAM: 32 GB per node

To compare, the same testing showed repeated upgrade failures for MKE 3 systems with 2 vCPUs and 8 GB of RAM.


## Software requirements

- Operating systems:
  - Ubuntu 22.04 Linux
  - RHEL 8.10
  - Rocky Linux 9.4
- Architecture: `amd64`
- CNI: Calico

## Load balancer requirements

The load balancer can be implemented in many different ways. For example, you can use
HAProxy, NGINX, or the load balancer of your cloud provider.

To ensure the MKE 4k Dashboard functions properly, MKE 4k requires a TCP load balancer.
This load balancer acts as a single point of contact to access the controllers.
With the default MKE 4k configuration, the load balancer must allow and route traffic
to each controller through the following ports:

| Listen port | Target port | Purpose             | Configurable         |
| ----------- | ----------- | ------------------- | -------------------- |
| 6443        | 6443        | Kubernetes API      | {{< icon "ban" >}}   |
| 9443        | 9443        | Controller join API | {{< icon "ban" >}}   |
| 443         | 33001       | Ingress Controller  | {{< icon "check" >}} |

You can configure the listen port of the Ingress Controller to be different from
the default port `443`. However, if you change the listen port, you must append
the new port number to the external address in the configuration file. For example,
if you set the listen port to be the same as the target port, `33001`, the configuration
should look as follows:

```yaml
apiServer:
  externalAddress: "mke.example.com:33001"
```

{{< callout type="important" >}}

The specified external address is automatically added to the SANs of the cluster certificate. If
the external address is not set, the first controller node of the cluster is
used, and anyone who visits their cluster through the address when the field is
not set will be denied access.

{{< /callout >}}

The target port must match the HTTPS node port of the Ingress Controller,
which is `33001` by default, but can be adjusted as needed. Refer to the configuration
details for `nodePorts` in the 
[Ingress Controller configuration](../../configuration/ingress#configuration).


