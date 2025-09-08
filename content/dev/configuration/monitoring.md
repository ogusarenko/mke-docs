---
title: Monitoring
weight: 4
---

The Monitoring add-on provides metrics collection, dashboards, and alerting
capabilities for MKE 4k clusters. It is built on the
[kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack),
and includes such components as Prometheus, Grafana, cAdvisor, and
supporting exporters.

## Configurable parameters

While a number of components are always enabled as they are required for core
monitoring, you can enable and disable others through configuration flags
under the ``monitoring`` section of the ``mke4k.yaml`` configuration file.

| Parameter 	| Type 	| Default 	| Description 	|
|---	|---	|---	|---	|
| ``enableGrafana`` 	| bool (optional) 	| ``true`` 	| Enables and disables Grafana deployment. 	|
| ``grafana.ingress.enabled``     	| bool 	| ``false`` 	| Creates an Ingress resource to expose Grafana (path ``/grafana``).<br><br>Effective only if ``enableGrafana`` is set to ``true``. 	|
| ``enableCAdvisor`` 	| bool 	| ``false`` 	| Controls how cAdvisor metrics are scraped. <dl>   <dt>``false``   <dd>Prometheus scrapes cAdvisor metrics through kubelet. </dd>  <dt>``true``   <dd>Kubelet scraping is disabled; assumes that a standalone cAdvisor service is available. </dd> </dl> 	|
| ``prometheus.nodeSelector`` 	| map[string]string 	| ``{}`` 	| Node selector labels applied to Prometheus Pods to control scheduling. For example, pinning Prometheus to dedicated monitoring nodes. 	|

Example ``mke4k.yaml`` configuration:

```
monitoring:
    enableGrafana: true
    grafana:
      ingress:
        enabled: false
    enableCAdvisor: false
    prometheus: {}
```

- ``enableGrafana: true`` → Grafana is deployed by default.
- ``grafana.ingress.enabled: false`` → Grafana is not exposed via Ingress by default.
- ``enableCAdvisor: false`` → Prometheus scrapes cAdvisor metrics via kubelet.
- ``prometheus: {}`` → Prometheus runs with default settings, as a nodeSelector
  is not applied.

## Stack components

The following tables offer detail for the components deployed by the MKE 4k
monitoring stack:

### Always enabled
| Component 	| Description 	|
|---	|---	|
| Prometheus 	| Core metrics collection engine. 	|
| Prometheus Operator 	| Manages Prometheus, AlertManager, and related CRDs. 	|
| Kube State Metrics 	| Exposes Kubernetes object and cluster state metrics. 	|
| Node Exporter 	| Provides node-level metrics. 	|

### Optional

Optional stack components are exposed in the ``monitoring`` section of the ``mke4k.yaml`` configuration file.

| Component 	| Description 	|
|---	|---	|
| Grafana 	| Dashboarding and visualization. Controlled by the ``enableGrafana`` parameter. Ingress exposure is controlled by the ``grafana.ingress.enabled`` parameter.
| Kubelet cAdvisor metrics 	| Enabled by default; disabled if ``enableCAdvisor`` is set to ``true``. {{< callout type="info" >}}If disabled, MKE 4k expects a standalone cAdvisor service.{{< /callout >}} 	|

## Component Detail

| Component 	| Enabled / Disabled 	| Notes 	| How to Toggle 	|
|---	|---	|---	|---	|
| Prometheus 	| ✅ Always enabled 	| Includes an additional ServiceMonitor for k0s-observability pushgateway. Configured to discover ServiceMonitors across all namespaces. 	| Not applicable. Always on. 	|
| PrometheusOperator 	| ✅ Always enabled 	| Manages Prometheus and AlertManager CRDs. Admission webhook patch job also tolerates master taints. 	| Not applicable. Always on. 	|
| KubeStateMetrics 	| ✅ Always enabled 	| Exposes cluster state metrics. Runs with tolerations for master node taints. 	| Not applicable. Always on. 	|
| Node Exporter 	| ✅ Always enabled 	| Deployed as a DaemonSet (``monitoring-prometheus-node-exporter-*``), one Pod per node. Provides node-level metrics (CPU, memory, filesystem, network). Scraped by Prometheus. 	| Not applicable. Always on. 	|
| Grafana 	| ➡️ Controlled by ``monitoring.enableGrafana`` 	| Provides dashboards. If enabled, tolerates master taints. Ingress can also be enabled separately. 	| monitoring.enableGrafana: true/false  monitoring.grafana.ingress.enabled: true/false 	|
| Kubelet ServiceMonitor (cAdvisor) 	| ✅ Enabled by default.<br>❌ Disabled when ``monitoring.enableCAdvisor`` is ``true``. 	| Uses the cAdvisor built into kubelet, unless an external standalone cAdvisor service is enabled. 	| monitoring.enableCAdvisor: true/false 	|

## Access Prometheus

[Prometheus](https://prometheus.io/) is an open-source monitoring and alerting
toolkit, designed for reliability and scalability, that collects and stores metrics
as time series data. It offers powerful query capabilities and a flexible alerting system.

The Prometheus API is available at `https://<mke4_url>/prometheus/`

To access the Prometheus dashboard:

1. Port forward Prometheus:

    ```bash
    kubectl --namespace mke port-forward svc/prometheus-operated 9090
    ```

2. Navigate to `http://localhost:9090`.

## Access Grafana

[Grafana](https://grafana.com/) is an open-source monitoring platform that provides a rich set of tools for visualizing time-series data. It
includes a variety of graph types and dashboards.

Grafana is enabled in MKE 4k by default and may be disabled through the `mke4.yaml` configuration file:

```yaml
monitoring:
  enableGrafana: true
```

To access the Grafana dashboard:

1. Obtain the `admin` user password for the Grafana dashboard from the `monitoring-grafana` secret in the `mke` namespace.

   ```bash
   kubectl get secret monitoring-grafana -n mke -o jsonpath="{.data.admin-password}" | base64 --decode
   ```

2. Port forward Grafana:

    ```bash
    kubectl --namespace mke port-forward svc/monitoring-grafana 3000:80
    ```

3. Navigate to `http://localhost:3000` to access the **Welcome to Grafana** login page.

4. Enter the default username **admin** into the **Email or username** field and type the password you retrieved from the `monitoring-grafana` secret into the **Password** field.

5. Click **Log In**.

## cAdvisor

[cAdvisor](https://github.com/google/cadvisor) is an open-source tool that collects, aggregates, processes,
and exports information in reference to running containers.

cAdvisor is disabled in MKE 4k by default. You can enable the tool through the `mke4.yaml` configuration file:

```yaml
monitoring:
  enableCAdvisor: true
```

## MKE version comparison: Configuration parameters

| MKE 3 parameter 	| MKE 4k 	|
|---	|---	|
| ``cluster_config.node_exporter_port`` 	| Not supported 	|
| ``cluster_config.prometheus_memory_limit`` 	| Not supported 	|
| ``cluster_config.prometheus_memory_request`` 	| Not supported 	|
| ``cluster_config.metrics_scrape_interval`` 	| Not supported 	|
| ``cluster_config.metrics_retention_time`` 	| Not supported 	|