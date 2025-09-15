---
title: Infrastructure options
weight: 1
---

The `MkeChildConfig` object offers an `infrastructure` section that
is specific to child cluster configuration, an example of which follows, along
with descriptions for each section field:


```yaml
spec:
  infrastructure:
    provider: aws
    credential: my-aws-credential
    region: eu-west-1
    configuration:
```

| Parameter 	| Description 	|
|---	|---	|
| `provider` 	| Cloud provider. Currently, only `aws` is supported. 	|
| `credential` 	| The [k0rdent Credential](https://docs.k0rdent-enterprise.io/v1.1.0/admin/access/credentials/) object that is used to bootstrap and manage a child cluster. 	|
| `region` 	| Cloud provider region. 	| 
| `configuration` 	| Optional. A dynamic field that is used to customize infrastructure with values from the corresponding `ClusterTemplate` of the vendor that is specified in `provider`.<br><br>Detail on the values for the vendors supported by MKE 4k is available in the section that immediately follows. 	|

## `configuration` field options, by provider

The `infrastructure.configuration` field is dynamic, with values determined
according to the provider indicated in the `infrastructure.provider` field.

To view the `infrastructure.configuration` field values for a specific cloud
provider, click below on the name of that provider.
<br><br>
<details>
<summary><b>AWS</b></summary>

The AWS-specific values for the `configuration` field, when `provider: aws`,
are detailed below:

```yaml
controlPlane: # @schema description: The configuration of the control plane machines; type: object
  amiID: "" # @schema description: The ID of Amazon Machine Image; type: string
  iamInstanceProfile: control-plane.cluster-api-provider-aws.sigs.k8s.io # @schema description: A name of an IAM instance profile to assign to the instance; type: string; required: true
  instanceType: "m5.xlarge" # @schema description: The type of instance to create. Example: m4.xlarge; type: string; required: true
  rootVolumeSize: 32 # @schema description: Specifies size (in Gi) of the root storage device. Must be greater than the image snapshot size or 8 (whichever is greater); type: integer; minimum: 8
  imageLookup: # @schema description: AMI lookup parameters; type: object
    format: "amzn2-ami-hvm*-gp2" # @schema description: The AMI naming format to look up the image for this machine. It will be ignored if an explicit AMI is set; type: string; required: true
    org: "137112412989" # @schema description: The AWS Organization ID to use for image lookup if AMI is not set; type: string; required: true
    baseOS: "" # @schema description: The name of the base operating system to use for image lookup the AMI is not set; type: string
  uncompressedUserData: false # @schema description: Specify whether the user data is gzip-compressed before it is sent to ec2 instance. Cloud-init has built-in support for gzip-compressed user data. User data stored in aws secret manager is always gzip-compressed; type: boolean
  nonRootVolumes: [] # @schema title: Non-root storage volumes; description: Configuration options for the non root storage volumes, format: https://pkg.go.dev/sigs.k8s.io/cluster-api-provider-aws/v2/api/v1beta2#AWSMachineSpec.NonRootVolumes; type: array; item: object

worker: # @schema description: The configuration of the worker machines; type: object
  amiID: "" # @schema description: The ID of Amazon Machine Image; type: string
  iamInstanceProfile: control-plane.cluster-api-provider-aws.sigs.k8s.io # @schema description: A name of an IAM instance profile to assign to the instance; type: string; required: true
  instanceType: "m5.large" # @schema description: The type of instance to create. Example: m4.xlarge; type: string; required: true
  rootVolumeSize: 32 # @schema description: Specifies size (in Gi) of the root storage device. Must be greater than the image snapshot size or 8 (whichever is greater); type: integer; minimum: 8
  imageLookup: # @schema description: AMI lookup parameters; type: object
    format: "amzn2-ami-hvm*-gp2" # @schema description: The AMI naming format to look up the image for this machine. It will be ignored if an explicit AMI is set; type: string; required: true
    org: "137112412989" # @schema description: The AWS Organization ID to use for image lookup if AMI is not set; type: string; required: true
    baseOS: "" # @schema description: The name of the base operating system to use for image lookup the AMI is not set; type: string
  uncompressedUserData: false # @schema description: Specify whether the user data is gzip-compressed before it is sent to ec2 instance. Cloud-init has built-in support for gzip-compressed user data. User data stored in aws secret manager is always gzip-compressed; type: boolean
  nonRootVolumes: [] # @schema title: Non-root storage volumes; description: Configuration options for the non root storage volumes, format: https://pkg.go.dev/sigs.k8s.io/cluster-api-provider-aws/v2/api/v1beta2#AWSMachineSpec.NonRootVolumes; type: array; item: object

publicIP: false # @schema description: Specifies whether the instance should get a public IP; type: boolean
sshKeyName: "" # @schema description: The name of the key pair to securely connect to your instances. Valid values are empty string (do not use SSH keys), a valid SSH key name, or omitted (use the default SSH key name); type: [string, null]
bastion: # @schema description: The configuration of the bastion host; type: object
  enabled: false # @schema description: Allows this provider to create a bastion host instance with a public ip to access the VPC private network; type: boolean; required: true
  disableIngressRules: false # @schema description: Ensures ensure there are no Ingress rules in the bastion host's security group. Requires allowedCIDRBlocks to be empty; type: boolean
  allowedCIDRBlocks: [] # @schema description: A list of CIDR blocks allowed to access the bastion host. They are set as ingress rules for the Bastion host's Security Group (defaults to 0.0.0.0/0); type: array; item: string
  instanceType: t2.micro # @schema description: Use the specified instance type for the bastion. If not specified, Cluster API Provider AWS will use t3.micro for all regions except us-east-1, where t2.micro will be the default; type: string
  ami: "" # @schema description: Uses the specified AMI to boot the bastion. If not specified, the AMI will default to one picked out in public space; type: string

clusterLabels: {} # @schema description: Labels to apply to the cluster; type: object; additionalProperties: true
clusterAnnotations: {} # @schema description: Annotations to apply to the cluster; type: object; additionalProperties: true

controlPlaneNumber: 3 # @schema description: The number of the control-plane machines; minimum: 1; type: integer; required: true
workersNumber: 2 # @schema description: The number of the worker machines; minimum: 1; type: integer; required: true
```

Example configuration:

```yaml
apiVersion: mke.mirantis.com/v1alpha1
kind: MkeChildConfig
metadata:
  name: <name_of_your_child_cluster>
  namespace: k0rdent
spec:
  version: v4.1.1
  infrastructure:
    provider: aws
    credential: aws-cluster-identity-cred
    region: us-west-2
    configuration:
      controlPlaneNumber: 1
      workersNumber: 1
```

</details>