---
title: kubelogin Setup
weight: 4
---

[kubelogin](https://github.com/int128/kubelogin) is a helpful open-source tool
that you can use to authenticate and set up a kubeconfig file for MKE 4k, with
which you can add an authentication client application and audience to your MKE
4k cluster.

{{< callout type="info" >}}

The kubectl command line tool is used in the kubelogin setup procedure
described herein. You can, however use other such tools, by changing the exec
commands as appropriate.

{{< /callout >}}

**To configure kubelogin:**

1. Enable `kubelogin` by changing the `authentication.kubelogin` parameter in
   the `mke4.yaml` configuration file to `true`. For security purposes, `kubelogin` is disabled by default.

   ``` yaml
    authentication:
      kubelogin: true
    ```

   MKE 4k will then generate the necessary audience and client application for
   kubelogin function. Be aware, though, that if later on you need to customize
   the setup, you must create a separate
   [audience](../../authentication/#add-audiences) and
   [client](../../authentication/#add-third-party-client-applications) in your
   MKE 4k configuration.

2. Set up `kubelogin` on your local machine:

   ```
   kubectl oidc-login setup \
     --oidc-issuer-url=<cluster's external URL>/dex \
     --oidc-client-id=kubelogin \
     --insecure-skip-tls-verify <-- should be used for testing only
   ```

   Running this command triggers the authentication flow and opens a browser
   window on your machine from where you can enter your credentials.

3. Set up your kubeconfig:

   ```
   kubectl config set-credentials oidc \
   --exec-interactive-mode=Never \
   --exec-api-version=client.authentication.k8s.io/v1 \
   --exec-command=kubectl \
   --exec-arg=oidc-login \
   --exec-arg=get-token \
   --exec-arg=--oidc-issuer-url=<cluster's external address>/dex \
   --exec-arg=--oidc-client-id=kubelogin \
   --exec-arg=--oidc-extra-scope=email \
   --exec-args=--insecure-skip-tls-verify <-- should be used for testing only
   ```

   Thereafter, you can use `kubectl` with the generated token:

   ```
   kubectl --user=oidc get pods -A
   ```

   {{< callout type="info" >}}

   If you have not yet authenticated or the token is expired, a browser window
   will open, from which you can proceed with the authentication process.

   {{< /callout >}}

4. To switch `kubectl` to always use the oidc context:

   ```
   kubectl config set-context --current --user=oidc
   ```

Refer to the official [kubelogin documentation](https://github.com/int128/kubelogin) for comprehensive information.
