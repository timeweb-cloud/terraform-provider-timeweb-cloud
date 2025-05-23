---
page_title: "twc_lb Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed load balancer and provides actual information about its status
---

# twc_lb (Resource)

Resource for describing needed load balancer and provides actual information about its status

## Примеры использования

```terraform
// Select balancer preset that may process 10K RPS with price between 100 and 200 RUB
data "twc_lb_preset" "example-lb-preset" {
  requests_per_second = "10K"

  price_filter {
    from = 100
    to = 200
  }
}

// Create balancer example
resource "twc_lb" "example-lb" {
  name = "example-lb"

  algo = "roundrobin"

  is_sticky = false
  is_use_proxy = false
  is_ssl = false
  is_keepalive = false

  health_check {
    proto = "http"

    port = 80

    path = "/lala"

    inter = 10
    timeout = 5
    fall = 3
    rise = 2
  }

  ips = []

  preset_id = data.twc_lb_preset.example-lb-preset.id

  project_id = resource.twc_project.example-project.id
}

// Add rules to created balancer
resource "twc_lb_rule" "example-lb-rule" {
  lb_id = resource.twc_lb.example-lb.id

  balancer_proto = "http2"
  balancer_port = 83
  server_proto = "http"
  server_port = 82
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `name` (String) Name for balancer
- `preset_id` (Number) Preset ID for balancer

### Optional

- `algo` (String) Algorithm for balancing
- `availability_zone` (String) Availability zone for load balancer
- `client_timeout` (Number) Client timeout
- `connect_timeout` (Number) Connection timeout
- `floating_ip_id` (String) Floating IP ID for server
- `health_check` (Block List, Max: 1) (see [below for nested schema](#nestedblock--health_check))
- `httprequest_timeout` (Number) Http request timeout
- `ips` (Set of String) Backends IPs
- `is_keepalive` (Boolean) Keep alive connection from balancer to backend server
- `is_ssl` (Boolean) Automatic redirect HTTP to HTTPS
- `is_sticky` (Boolean) Save user session for balancing to same backend server
- `is_use_proxy` (Boolean) Use PROXY-protocol for communicating with backend server
- `local_network` (Block List, Max: 1) Flag that enables local network for load balancer (see [below for nested schema](#nestedblock--local_network))
- `maxconn` (Number) Maximum number of connections to backend server
- `project_id` (Number) Project ID for created balancer
- `server_timeout` (Number) Server timeout

### Read-Only

- `id` (String) The ID of this resource.
- `ip` (String) IP-address balancer of network interface
- `local_ip` (String) Local IP-address balancer of network interface
- `status` (String) Current status of balancer (`started`, `starting`, `stoped`, `no_paid`)

<a id="nestedblock--health_check"></a>
### Nested Schema for `health_check`

Optional:

- `fall` (Number) Error requests count threshold for active backend health check
- `inter` (Number) Interval in seconds for active backend health check
- `path` (String) Path for active backend health check
- `port` (Number) TCP port for active backend health check
- `proto` (String) Protocol for active backend health check
- `rise` (Number) Success requests count threshold for active backend health check
- `timeout` (Number) Timeout for active backend health check


<a id="nestedblock--local_network"></a>
### Nested Schema for `local_network`

Required:

- `id` (String) ID of VPC for assign address from

Optional:

- `ip` (String) Address in VPC subnetwork for manual assign

