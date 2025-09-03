---
title: SAML
weight: 2
---

You can configure SAML (Security Assertion Markup Language) for MKE 4k through
the `authentication.saml` section of the `mke4.yaml` configuration file.

SAML example configuration:

```yaml
authentication:
  saml:
    enabled: true
    ssoURL: https://dev64105006.okta.com/app/dev64105006_mke4saml_1/epkdtszgindywD6mF5s7/sso/saml
    usernameAttr: name
    emailAttr: email
```

## Configure SAML service for MKE

In the `mke4.yaml` configuration file `authentication.saml` section, enable your
SAML service by setting `enabled` to `true`. Use the remaining fields, which
are defined in the following table, to configure your chosen SAML provider.

{{< callout type="info" >}} For information on how to obtain the field values,
refer to [Setting up Okta as a SAML provider](../../../tutorials/authentication-provider-setup/setting-up-okta-as-a-saml-provider).
{{< /callout >}}

| Field                             | Description                                                                                                                                                                                                |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enabled`                         | Enable authentication through dex.                                                                                                                                                                         |
| `ssoMetadataURL`                  | Metadata URL provided by some IdPs, with which MKE 4k can retrieve information for all other SAML configurations.<br>{{< callout type="info" >}}When a URL is provided for `ssoMetadataURL`, the other SAML fields are not required. With the exception of the ``ca`` field, however, users can specify other fields along with the ``ssoMetadataURL`` field, to allow for the support of more IdPs that require users to manually configure their SAML service.

{{< /callout >}} <br>Similar to the other authentication fields detailed here, which are now hidden in the cluster once they have been configured, ``ssoMetadataURLhas`` has been placed in this category due to the availability of the raw cert at the endpoint. When a user configures their cluster using ``ssoMetadataURL``, it is removed from the cluster version of the configuration and setup to look the same as if the user had manually configured SAML. (``caData`` is hidden. All other fields are allowed). |
| `ca`                              | The path to the certificate authority file within the Dex Pod. Must be manually mounted. Alternative to ``CaData`` field.|
| `caData` 	| The raw certificate authority data. The ``caData`` field will be hidden once applied to the cluster. Alternative to ``ca`` field.  	|
| `ssoURL`                          | URL to provide to users to sign into MKE 4k with SAML. Provided by the IdP.                                                                                                                                 |
| `insecureSkipSignatureValidation` | Optional. Use to skip the signature validation. For testing purposes only.                                                                                                                                 |
| `usernameAttr`                    | Username attribute in the returned assertions, to map to ID token claims.                                                                                                                                  |
| `emailAttr`                       | Email attribute in the returned assertions, to map to ID token claims.                                                                                                                                     |
| `groupsAttr`                      | Optional. Groups attribute in the returned assertions, to map to ID token claims.                                                                                                                          |
| `entityIssuer`                    | Optional. Include as the Issuer value during authentication requests.                                                                                                                                      |
| `ssoIssuer`                       | Optional. Issuer value that is expected in the SAML response.                                                                                                                                              |
| `groupsDelim`                     | Optional. If groups are assumed to be represented as a single attribute, this delimiter splits the attribute value into multiple groups.                                                                   |
| `nameIDPolicyFormat`              | Requested name ID format.                                                                                                                                                                                  |

## Test authentication flow

1. Navigate to `http://<MKE 4k hostname>/dex/login`.
2. Click **Login** to display the login page.
3. Select **Log in with SAML**.
4. Enter your credentials and click **Sign In**. If authentication is
   successful, you will be redirected to the client applications home page.
