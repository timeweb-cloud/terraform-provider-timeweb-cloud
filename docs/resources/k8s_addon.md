---
page_title: "twc_k8s_addon Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing K8S cluster addon
---

# twc_k8s_addon (Resource)

Resource for describing K8S cluster addon

## Примеры использования

```terraform
resource "twc_k8s_addon" "example-capsule-addon" {
  cluster_id = twc_k8s_cluster.example-k8s-cluster.id
  type = "capsule"
  version = "v0.7.2"
  config_type = "custom"
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `cluster_id` (Number) K8S cluster ID for which addon should be created
- `config_type` (String) Config type (`basic`, `custom`)
- `type` (String) Type of addon (`ingress`, `kubernetes_dashboard`, `csi_s3`, `flannel`, `cilium`, `kube-prometheus-stack`, `kiali`, `capsule`, `jaeger`, `cert-manager`, `istio-ingress`, `istiod`, `istio-base`, `traefik`, `fluent-operator`, `velero`, `external-dns`, `nvidia`)

### Optional

- `config` (Block List, Max: 1) (see [below for nested schema](#nestedblock--config))
- `version` (String) Version of the config
- `yaml_config` (String) YAML configuration for addon

### Read-Only

- `id` (String) ID of the addon
- `status` (String) Status of added cluster addon

<a id="nestedblock--config"></a>
### Nested Schema for `config`

Required:

- `access_key` (String) Access key of the config
- `endpoint` (String) Endpoint of the config
- `name` (String) Name of the config
- `region` (String) Region of the config
- `secret_key` (String) Secret key of the config

