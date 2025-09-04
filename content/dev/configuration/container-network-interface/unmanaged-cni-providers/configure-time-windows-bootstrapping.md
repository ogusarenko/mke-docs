---
title: Configure time windows for network bootstrapping
weight: 6
---

Once the initial bootstrapping of the underlying Kubernetes provider k0s is
complete, MKE 4k confirms that the kube-apiserver is reachable. It then waits
for cluster networking to become functional before proceeding with the
installation. It is during this time window that the unmanaged CNI installation
establishes cluster networking.

The parameters detailed below can be used with mkectl to configure various
parameters related to the installation of unmanaged CNIs:

| Parameter 	| Description 	|
|---	|---	|
| ``--timeout-minutes`` 	| Configures the timeout around the totality of installation time, which has a default value of 60 minutes. 	|
| ``--upgrade-timeout-minutes`` 	| Configures the timeout around the totality of upgrade time, which has a default value of 60 minutes. If the upgrade is not finished successfully in this period, MKE will roll the installation back to the original version. 	|
| ``--cni-check-timeout`` 	| Configures the total time in minutes that MKE 4k
will wait for cluster networking verification. The default value is 10 minutes.
Note that this value cannot be greater than that established by the
``--timeout-minutes`` parameter. 	|
| ``--cni-check-port`` 	| Configures the port number used to verify that cluster networking has been established. The default is http port 80. This port must be available and unused on the first two manager nodes/hosts and first two worker nodes/hosts, as listed in the mke4 configuration yaml. Each of these four nodes should also be open to accept connections from any of the other nodes, as well as from the cluster ipv4 CIDR. This check is only made during the installation phase, and this port can be blocked following a successful installation or upgrade. 	|
