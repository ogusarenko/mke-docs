---
title: Upgrade compatibility checks
weight: 3
---

Compatibility checks run during any upgrade from an MKE 3 system to an MKE 4k
system, following which they report any enabled settings that are not supported
in MKE 4k. These checks serve to make users aware of any features in the source
MKE system that will not be available in the target MKE 4k system.

{{< callout type="info" >}}

An alternative `check` command is available that does not connect to a running
cluster and which only runs the compatibility checks against a local MKE 3
configuration.

```
mkectl check mke3 --output=. --mke3-config-path <path-to-file>
```

{{< /callout >}}

## How the checks work

The `upgrade` command pulls the configuration from a running MKE 3 cluster and
checks for upgrade compatibility. These checks can be done without performing
an upgrade by including the `--dry-run` flag.

The compatibility checks process is as follows:

1. Examination of all of the settings in a source MKE 3 configuration, to
   determine whether they are enabled. Settings that are not enabled are
   skipped.

2. Examination of all of the enabled MKE 3 source settings to determine whether
   they each have a correlating MKE 4k setting. Those that do have a correlating
   setting are marked as supported.

3. Two compatibility checks reports are output to the user: the original MKE
   configuration file with the settings sorted and a human-readable version.
   Both of these reports group the source MKE 3 system settings according to
   their status: `supported`, `not supported`, and `skipped`.

During an actual upgrade, any missing settings are output to the console with a
prompt. At that prompt, you can opt to either halt the upgrade or continue the
upgrade with failing checks.

{{< callout type="info" >}}

You can circumvent the compatibility check prompt by using the `--force` flag when you issue the `mkectl upgrade` command.

{{< /callout >}}

When the compatibility checks are used with the `--output` flag, a folder is
created in the specified output path (if no path is specified, the check will
output to the XDG directory). This folder contains the different outputs, with
the name changing depending on which command was run. With the `upgrade` command, the folder is named `mke-upgrade`.

Example `upgrade` command:

```
mkectl upgrade --dry-run --output=<output path> <additional-upgrade-arguments>
```

## Output Examples

Click the headings below to view example output for each of the upgrade compatibility checks:

<br>
<details>

<summary><b>Sorted MKE configuration file output</b></summary>

```
###################################
# MKE3->MKE4 Compatibility Checks #
###################################


#################
# Not supported #
#################

[auth]
  default_new_user_role = "restrictedcontrol"
  managedPasswordFallbackUser = ""
  [auth.external_identity_provider]
    caBundle = ""
    httpProxy = ""
    httpsProxy = ""
    userServiceId = "4dcdace6-4eb4-461d-892f-01aed344ac80"
    usernameClaim = "email"
    wellKnownConfigUrl = "https://example.com/.well-known/openid-configuration"
  [auth.samlProxy]
    enabled = true
    proxy_url = "https://proxy.com/"
    [auth.samlProxy.credentials]
      [auth.samlProxy.credentials.basic]
        user = "mke"
        password = "verysecret"
  [auth.sessions]
    per_user_limit = 10

[backup_schedule_config]
  backups_limit = 30
  cron_spec = "0 0 0 * * *"
  enabled = true
  include_logs = true
  no_passphrase = false
  notification_delay = 7
  passphrase = "verysecretphrase"
  path = "/tmp"

[cluster_config]
  azure_ip_count = ""
  calico_kdd = false
  calico_strict_affinity = false
  calico_vxlan = false
  calico_vxlan_mtu = "1450"
  calico_vxlan_port = "4789"
  cipher_suites_for_etcd_server = ""
  containerd_root = "/var/lib/containerd"
  controller_port = 443
  cpu_usage_banner_scrape_interval = "10m"
  cpu_usage_banner_threshold = 20
  custom_kube_controller_manager_flags = ["--service-sync-period=5m0s"]
  etcd_storage_quota = "8GB"
  exclude_server_identity_headers = true
  hide_swarm_ui = false
  image_prune_schedule = "0 0 0 * * *"
  image_scan_cvss_version = 3
  ipip_mtu = "1480"
  iscsi_enabled = false
  iscsiadm_path = ""
  iscsidb_path = ""
  kube_api_server_audit_log_maxbackup = 10
  kube_api_server_audit_log_maxsize = 10
  kube_api_server_auditing = true
  kube_api_server_request_timeout = "1m0s"
  kube_apiserver_port = 6443
  kube_controller_manager_profiling_enabled = true
  kube_manager_terminated_pod_gc_threshold = 12500
  kv_snapshot_count = 20000
  kv_timeout = 5000
  metrics_retention_time = ""
  metrics_scrape_interval = "1m"
  networks_quota_per_collection = 0
  prometheus_memory_request = ""
  rethinkdb_cache_size = "16GB"
  storage_expt_enabled = false
  swarm_only = false
  swarm_polling_disabled = false
  swarm_port = 2376
  swarm_strategy = "spread"
  unauthenticated_http2_dos_mitigation = false
  use_flex_volume_driver = false
  vxlan_vni = 10000
  windows_containerd_root = "C:\\ProgramData\\containerd"
  windows_kubelet_data_root = "C:\\k"
  [cluster_config.policy_enforcement]
    pod_security_policy = true

[custom_api_server_headers]

[etcd_cleanup_schedule_config]

[license_configuration]
  auto_refresh = true
  omit_telemetry = false

[pki]
  client_ca_bundle = ""

[scheduling_configuration]
  default_node_orchestrator = "swarm"
  enable_admin_ucp_scheduling = true

[user_workload_defaults]
  [user_workload_defaults.swarm_defaults]
    [user_workload_defaults.swarm_defaults."tasktemplate.restartpolicy.delay"]
      value = "5m"
    [user_workload_defaults.swarm_defaults."tasktemplate.restartpolicy.maxattempts"]
      value = "3"

#############
# Supported #
#############

[auth]
  managedPasswordDisabled = false
  [auth.external_identity_provider]
    clientId = "4dcdace6-4eb4-461d-892f-01aed344ac80"
    issuer = "https://example.com"
  [auth.sessions]
    lifetime_minutes = 60
    renewal_threshold_minutes = 20

[cluster_config]
  calico_ip_auto_method = ""
  calico_mtu = "1480"
  cipher_suites_for_kube_api_server = ""
  cipher_suites_for_kubelet = ""
  cluster_name = "kubernetes"
  custom_kubelet_flags = ["--http-check-frequency=20s", "--kube-api-qps=200", "--kube-api-burst=200"]
  kube_api_server_audit_log_maxage = 30
  kube_proxy_mode = "iptables"
  kube_scheduler_profiling_enabled = true
  kubelet_data_root = "/var/lib/kubelet"
  kubelet_event_record_qps = 50
  kubelet_max_pods = 110
  manager_kube_reserved_resources = "cpu=1,memory=2Gi,ephemeral-storage=4Gi"
  nodeport_range = "32768-35535"
  pod_cidr = "192.168.0.0/16"
  prometheus_memory_limit = ""
  service_cluster_ip_range = "10.96.0.0/16"
  worker_kube_reserved_resources = "cpu=100m,memory=300Mi,ephemeral-storage=500Mi"

[log_configuration]
  level = "DEBUG"

######################
# Disabled (skipped) #
######################

[audit_log_configuration]
  level = ""
  support_bundle_include_audit_logs = false

[auth]
  backend = "managed"
  samlEnabled = false
  scimEnabled = false
  [auth.account_lock]
    enabled = false
  [auth.sessions]
    store_token_per_session = false

[cluster_config]
  cadvisor_enabled = false
  calico_controller_liveness_probe_failure_threshold = 0
  calico_controller_liveness_probe_initial_delay_seconds = -1
  calico_controller_liveness_probe_period_seconds = 0
  calico_controller_liveness_probe_success_threshold = 0
  calico_controller_liveness_probe_timeout_seconds = 0
  calico_controller_probes_tuning = false
  calico_controller_readiness_probe_failure_threshold = 0
  calico_controller_readiness_probe_initial_delay_seconds = -1
  calico_controller_readiness_probe_period_seconds = 0
  calico_controller_readiness_probe_success_threshold = 0
  calico_controller_readiness_probe_timeout_seconds = 0
  calico_ebpf_enabled = false
  cloud_provider = ""
  cni_installer_url = ""
  custom_proxy_config_yaml = ""
  disable_http2 = false
  disable_seccomp_default = false
  external_service_lb = ""
  gmsa_allow_non_admins = false
  ignore_pod_updates_for_node_selector = false
  image_scan_aggregation_enabled = false
  k8s_always_pull_images_ac_enabled = false
  kms_enabled = false
  kube_default_drop_masq_bits = false
  kube_protect_kernel_defaults = false
  kube_proxy_no_cleanup_on_start = true
  kube_proxy_use_config_file = false
  kube_scheduler_bind_to_all = false
  kubelet_pods_per_core = 0
  local_volume_collection_mapping = false
  nvidia_device_plugin = false
  pre_logon_message = ""
  profiling_enabled = false
  pubkey_auth_cache_enabled = false
  secure_overlay = false
  unmanaged_cni = false
  [cluster_config.core_dns_lameduck_config]
    enabled = false
  [cluster_config.ingress_controller]
    enabled = false
  [cluster_config.k8s_event_rate_limit]
    event_rate_limit_ac_enabled = false
  [cluster_config.metallb_config]
    enabled = false
  [cluster_config.policy_enforcement]
    [cluster_config.policy_enforcement.gatekeeper]
      enabled = false

[hardening_configuration]
  hardening_enabled = false

[tracking_configuration]
  disable_tracking = false
  ```
</details>
<br>
<details>

<summary><b>Human readable output</b></summary>

```
`###################################
# MKE3->MKE4 Compatibility Checks #
###################################


#################
# Not supported #
#################

User workload defaults
auth
  Default new user role
  Managed password fallback user
  sessions
    Per user limit
  samlProxy
    SAML Proxy
    SAML proxy URL
    credentials
      SAML proxy credentials basic
  external_identity_provider
    External identity provider HTTP proxy
    External identity provider HTTPS proxy
    External identity provider user service ID
    External identity provider well known config URL
    External identity provider CA bundle
    External identity provider username claim
scheduling_configuration
  Admin Scheduling
  Swarm
license_configuration
  Auto refresh license
  Omit telemetry
custom_api_server_headers
  Custom API server headers name
  Custom API server headers value
pki
  Client CA bundle
Custom kube API server flags
Custom kube scheduler flags
backup_schedule_config
  Backup schedule passphrase
  Backup schedule no passphrase
  Backup schedule cron spec
  Backup schedule include logs
  Backup schedule backups limit
  Backup schedule notification delay
  Scheduled Backups
  Backup schedule path
etcd_cleanup_schedule_config
  Etcd cleanup min ttl to keep seconds
  Etcd cleanup cron expression
  Etcd cleanup defrag enabled
  Etcd cleanup defrag pause seconds
  Etcd cleanup defrag timeout seconds
  Etcd Cleanup
cluster_config
  Unauthenticated HTTP2 DOS mitigation
  policy_enforcement
    Policy enforcement pod security policy
  Calico VXLAN MTU
  Swarm polling disabled
  Windows containerd root
  Image scan CVSS version
  ISCSIdb path
  Calico VXLAN
  Cipher suites for etcd server
  Kube API server custom audit policy YAML
  Swarm only
  Configurable controller port
  Calico strict affinity
  VXLAN VNI
  Calico VXLAN port
  CPU usage banner scrape interval
  Kube API server enable custom audit policy
  Kube API server audit log max backup
  Hide swarm UI
  Etcd storage quota
  Azure IP count
  Networks quota per collection
  Kube API server request timeout
  Calico KDD
  Metrics retention time
  Metrics scrape interval
  Prometheus memory request
  Custom kube controller manager flags
  Windows GMSA
  Node local DNS
  Image prune schedule
  Swarm strategy
  Configurable containerd root
  Windows kubelet data root
  Use flex volume driver
  Configurable kube port
  KV timeout
  RethinkDB cache size
  Exclude server identity headers
  Storage export
  Swarm port
  KV snapshot count
  ISCSIadm path
  IPIP MTU
  Kube API server auditing
  Kube controller manager profiling enabled
  CPU usage banner threshold
  Kube API server audit log max size
  Configurable node export port
  ISCSI
  Kube manager terminated pod GC threshold

#############
# Supported #
#############

cluster_config
  Kube scheduler profiling enabled
  Node Port Range
  Custom kubelet flags
  Kube API server audit log max age
  Calico auto IP
  Kubelet max pods
  Cluster name
  Prometheus memory limit
  Pod CIDR
  Cipher suites for kubelet
  Manager kube reserved resources
  Kubelet event record QPS
  Service cluster IP range
  Cipher suites for kube API server
  Worker kube reserved resources
  Kubelet data root
  Calico MTU
  Kube Proxy Mode
auth
  Managed password disabled
  sessions
    Lifetime minutes
    Renewal threshold minutes
  external_identity_provider
    OIDC Authentication
    External identity provider client ID
log_configuration
  Log Level Configuration

##############################
# Disabled in MKE3 (Skipped) #
##############################

auth
  External authentication providers
  sessions
    Store token per session
  SAML Authentication
  SCIM Authentication
  account_lock
    Account Lock
tracking_configuration
  Tracking
audit_log_configuration
  Audit Log Configuration
  Include audit logs in support bundles
cluster_config
  KMS enabled
  K8s always pull images AC enabled
  core_dns_lameduck_config
    Core DNS lameduck config enabled
  Cloud provider
  Nvidia device plugin
  CAdvisor enabled
  policy_enforcement
    gatekeeper
      Policy enforcement gatekeeper enabled
  metallb_config
    Metallb config enabled
  Kube scheduler bind to all
  Secure overlay
  Calico controller readiness probe initial delay seconds
  Auth cache
  External service LB
  Calico liveness probe threshold
  Calico controller readiness probe timeout seconds
  Local volume collection mapping
  Calico controller readiness probe period seconds
  Unmanaged CNI
  Disable HTTP2
  Profiling
  Calico controller liveness probe timeout seconds
  Calico controller readiness probe failure threshold
  Kube proxy use config file
  GMSA allow non admins
  ingress_controller
    Ingress controller enabled
  k8s_event_rate_limit
    K8s event rate limit AC enabled
  CNI installer URL
  Calico controller liveness probe period seconds
  Calico controller liveness probe success threshold
  Custom proxy config YAML
  Kubelet pods per core
  Ignore pod updates for node selector
  Kube proxy no cleanup on start
  Kube protect kernel defaults
  Image Scan Aggregation
  Calico controller liveness probe initial delay seconds
  Kube default drop masq bits
  Pre logon message
  Calico controller probes tuning
  Calico controller readiness probe success threshold
  Disable seccomp default
  Calico EBPF
hardening_configuration
  Hardening
```

</details>
