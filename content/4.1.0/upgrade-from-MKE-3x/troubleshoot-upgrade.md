---
title: Troubleshoot the Upgrade
weight: 9
---

You can address various potential MKE upgrade issues using the tips and
suggestions detailed herein.

{{< callout type="important" >}}

The MKE 3 `etcdv3` backend is not supported for upgrade to MKE 4k.

{{< /callout >}}

During the upgrade from MKE 3 to MKE 4, which defaults to the `etcdv3`
backend, you may receive the following error:

```bash
mkectl upgrade --hosts-path hosts.yaml --mke3-admin-username admin --mke3-admin-password <mke_admin_password> -l debug --config-out new-mke4.yaml --external-address <mke4_external_address>
...
Error: unable to generate upgrade config: unsupported configuration for mke4 upgrade: mke3 cluster is using etcdv3 and not kdd backend for calico
```

To resolve the issue, ensure that:

- The MKE 3 source is an eligible MKE 3 cluster:

  - MKE 3.7.15 or later
  - Any MKE 3.8 release

- The `calico_kdd` flag in the MKE 3 configuration file is set to `true`.
- The configuration is applied to the MKE 3 cluster.

{{< callout type="info" >}}

A KDD mode upgrade is irreversible. Thus, to reduce risk, when upgrading
enterprise clusters, it is recommended that you work directly with Mirantis to
plan the process and monitor it through to completion.

{{< /callout >}}

Example output:

```bash
$ AUTHTOKEN=$(curl --silent --insecure --data '{"username":"'$MKE_USERNAME'","password":"'$MKE_PASSWORD'"}' https://$MKE_HOST/auth/login | jq --raw-output .auth_token)

$ curl --silent --insecure -X PUT -H "accept: application/toml" -H "Authorization: Bearer $AUTHTOKEN" --upload-file 'mke-config.toml' https://$MKE_HOST/api/ucp/config-toml
{"message":"Calico datastore upgrade from etcd to kdd successful"}
```

{{< callout type="important" >}}

Refer to the Upgrade Prerequisites for information on [how to migrate an etcd-backed cluster to KDD](../upgrade-prerequisites#calico-migration-kdd-etcd).

{{< /callout >}}
