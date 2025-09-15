---
title: Backup and restore options
weight: 4
---

Common configuration file section: `.spec.backup`

- Use `storage_provider.external_options.external_credentials_secret_name`
instead of `storage_provider.external_options.credentials_file_path` and
`storage_provider.external_options.credentials_file_profile`, with the name
of the secret that contains external storage provider credentials.

- Replace [Back up using an external storage
  provider](../../../configuration/backup-restore/external) steps 5-7 with the following:

  5. Create the secret object with credentials in the `k0rdent` namespace where
     the `MkeChildConfig` object will be applied:
       ```
        kubectl -n k0rdent create secret generic aws-credentials --from-literal=aws_access_key_id=<AWS_ACCESS_KEY_ID> --from-literal=aws_secret_access_key=<AWS_SECRET_ACCESS_KEY>
       ```
   6. Set the `storage_provider` section of the `MkeChildConfig`
      configuration file, adding the name of the secret with IAM credentials:

         ```yaml
           spec:
             backup:
               storage_provider:
                 type: External
                 external_options:
                   provider: aws
                   bucket: <BUCKET_NAME>
                   region: <BUCKET_REGION>
                   external_credentials_secret_name: aws-credentials
         ```
   7. Apply the configuration:

       ```
       kubectl apply -f mkechildconfig.yaml
       ```

Refer to [Backup and restore](../../../configuration/backup-restore/) for more details.
