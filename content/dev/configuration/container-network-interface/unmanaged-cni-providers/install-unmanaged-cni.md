---
title: Install an unmanaged CNI plugin
weight: 3
---

To install an unmanaged CNI plugin on a fresh install of MKE 4k:

1. Verify that your system meets all MKE 4k and third-party CNI plugin
   requirements.

2. Install MKE 4k with the custom provider as the only enabled provider in
   the install yaml file. This requires you to specify configuration for the mke
   cluster in yaml format and can be achieved using one of the below steps
   [3-5] depending on your circumstances.

   You can obtain yq at https://github.com/mikefarah/yq.

3. Create the yaml configuration file.

   1. Generate the yaml configuration:

      ```
      mkectl init| yq 'del(.spec.network.providers)'|yq
      '.spec.network.providers |= ([{"enabled": true, "provider": "custom"}])'
      ```

   2. Save the yaml configuration to a yaml file, for example ``mke4k.yaml``,
      and edit the file to set other desired configurations prior to using the
      file for installation.

   Alternatively, in the event that your final cluster configuration with all
   your customizations is already present in the ``mke4k.yaml`` yaml file but
   it is using the default CNI, you can change it to use an unmanaged CNI:

   ```
   yq 'del(.spec.network.providers)' mke4.yaml|yq '.spec.network.providers |=([{"enabled": true, "provider": "custom"}])' > mke4.yaml.tmp && mv mke4.yaml.tmp mke4k.yaml
   ```

   {{< callout type=info >}}

   Steps 3 and 4 assume that your installation employs MKE-s default
   IPv4 cluster ``CIDR[192.168.0.0/16]``. To set an alternative value, refer to
   the following example, which explicitly specifies ``192.158.0.0/16`` as the
   IPv4 cluster CIDR instead rather than the default:

   ```
   yq 'del(.spec.network.providers)'
   mke4.yaml|yq '.spec.network.providers |= ([{"enabled": true, "provider":
   "custom,", "extraConfig":{"cidrV4": "192.158.0.0/16"}}])' > mke4.yaml.tmp && mv
   mke4.yaml.tmp mke4.yaml
   ```

   {{< /callout >}}

5. Apply the ``mke4k.yaml`` yaml file to initiate MKE 4k installation:

   ```
   mkectl apply -f mke4.yaml <timeout-specifications>
   ```

   {{< callout type=info >}}

   For information on configuring appropriate timeout specifications, refer to
   [Configuring time windows for network
   bootstrapping](../configure-time-windows-bootstrapping).

   {{< /callout >}}

6. Once the installation reaches the point where cluster networking is being
   verified, install your chosen CNI. Look for the following indicators to
   know when this stage has been reached:

   ```
   INF k0s cluster installed
   ```

   ```
   INF using port 80 to verify networking
   ```

   ```
   INF waiting 10 minutes for cluster networking to be established using custom
   CNI provider
   ```

   {{< callout type=tip >}}

   Refer to [Considerations and Best
   Practices](../considerations-best-practices) for practical tips on how to
   successfully install an unmanaged CNI on an MKE 4k cluster.

   {{< /callout >}}

Once the unmanaged CNI is installed, has reached a state of being fully
functional, and MKE 4k has verified that cluster networking is in the desired
state, the installation will continue, as indicated:

```
INF Cleaning resources used for verifying networking
```
```
INF Confirmed available networking
```

Note that while the unmanaged CNI is being installed, MKE 4k constantly checks
whether cluster networking has been established. Thus, you need not be
concerned over whether the specification of a large timeout value will
needlessly add to cluster readiness wait time.
