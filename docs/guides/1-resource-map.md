---
page_title: "Resource and Data Source Map"
subcategory: "Guides"
description: |-
  A one-line map of every Timeweb Cloud resource and data source, how they group into products, and which data source feeds which argument.
---

# Resource and Data Source Map

This page is a quick index of everything the provider exposes. Use it to pick the
right resource, find the data source that produces an ID you need, and understand
how the pieces fit together. Every entry links to its own page for full arguments.

> Naming: all resources and data sources are prefixed with `twc_`. Managed
> resources are declared with `resource "twc_..."`; read-only lookups with
> `data "twc_..."`.

## How IDs flow between resources

Most resources take an ID that comes from a **data source** (a catalog lookup) or
from another **resource**. You almost never hard-code these IDs — you reference them.

| Argument | Where it comes from |
|---|---|
| `twc_server.os_id` | `data.twc_os.<x>.id` (or `data.twc_software.<x>.os[0].id`) |
| `twc_server.software_id` | `data.twc_software.<x>.id` |
| `twc_server.configuration.configurator_id` | `data.twc_configurator.<x>.id` |
| `twc_server.preset_id` | `data.twc_presets.<x>.id` |
| `twc_server.ssh_keys_ids` | `[twc_ssh_key.<x>.id]` |
| `twc_server.project_id` | `twc_project.<x>.id` or `data.twc_projects.<x>.id` |
| `twc_server.local_network.id` | `twc_vpc.<x>.id` |
| `twc_database_cluster.preset_id` | `data.twc_database_preset.<x>.id` |
| `twc_database_cluster.network.id` | `twc_vpc.<x>.id` |
| `twc_database_user.cluster_id` / `twc_database_backup*.cluster_id` | `twc_database_cluster.<x>.id` |
| `twc_k8s_cluster.preset_id` | `data.twc_k8s_preset.<x>.id` (`type = "master"`) |
| `twc_k8s_node_group.cluster_id` | `twc_k8s_cluster.<x>.id` |
| `twc_k8s_node_group.preset_id` | `data.twc_k8s_preset.<x>.id` (`type = "worker"`) |
| `twc_k8s_addon.cluster_id` | `twc_k8s_cluster.<x>.id` |
| `twc_lb.preset_id` | `data.twc_lb_preset.<x>.id` |
| `twc_lb_rule.lb_id` | `twc_lb.<x>.id` |
| `twc_lb.local_network.id` | `twc_vpc.<x>.id` |
| `twc_network_drive.preset_id` | `data.twc_network_drive_preset.<x>.id` |
| `twc_router.preset_id` | `data.twc_router_preset.<x>.id` |
| `twc_router.networks.id` | `twc_vpc.<x>.id` |
| `twc_firewall.link.id` | `twc_server.<x>.id` / `twc_database_cluster.<x>.id` / `twc_lb.<x>.id` |
| `twc_firewall_rule.firewall_id` | `twc_firewall.<x>.id` |
| `twc_floating_ip.resource.id` | `twc_server.<x>.id` / `twc_lb.<x>.id` / `twc_database_cluster.<x>.id` |
| `twc_dns_rr` (subdomain of a zone) | `data.twc_dns_zone.<x>` |
| `twc_s3_bucket.preset_id` | `data.twc_s3_preset.<x>.id` |

## Compute — Cloud Servers (VDS)

| Resource | Purpose |
|---|---|
| `twc_server` | A cloud server (VDS). Created from an OS, image, software, or by cloning; sized by a configurator or a preset. |
| `twc_server_ip` | An additional public IP (`ipv4`/`ipv6`) attached to a server. |
| `twc_server_disk` | An additional disk attached to a server. |
| `twc_server_disk_backup` | A one-off backup of a server disk. |
| `twc_server_disk_backup_schedule` | Automatic backup schedule for a server disk. |
| `twc_ssh_key` | An SSH public key that can be injected into servers. |

Data sources: `twc_configurator` (flexible CPU/RAM/disk within limits), `twc_presets`
(fixed plans), `twc_os` (operating systems), `twc_software` (preinstalled software + its OS),
`twc_ssh_keys` (existing keys), `twc_image` (custom images).

## Managed Databases (DBaaS)

| Resource | Purpose |
|---|---|
| `twc_database_cluster` | A managed database cluster (PostgreSQL, MySQL, Redis, etc.). |
| `twc_database_instance` | A logical database inside a cluster. |
| `twc_database_user` | A database user with privileges. |
| `twc_database_backup` | A one-off backup of a cluster. |
| `twc_database_backup_schedule` | Automatic backup schedule for a cluster. |

Data sources: `twc_database_preset` (plans, bound to `type` + `location`),
`twc_database_instance`, `twc_database_user`, `twc_database_replicas`, `twc_database_exporters`,
`twc_database_cluster`.

## Kubernetes (managed K8s)

| Resource | Purpose |
|---|---|
| `twc_k8s_cluster` | A managed Kubernetes control plane. |
| `twc_k8s_node_group` | A worker node group within a cluster. |
| `twc_k8s_addon` | A cluster add-on (e.g. `capsule`, `csi_s3`). |

Data sources: `twc_k8s_preset` (node plans; filter by `type = master|worker`),
`twc_k8s_configurator` (limits per node type).

## Networking

| Resource | Purpose |
|---|---|
| `twc_vpc` | A private network (VPC) in a location. |
| `twc_router` | A virtual router managing one or more VPC networks (DHCP, SNAT). |
| `twc_router_static_route` | A static route on a router. |
| `twc_router_dnat_rule` | A DNAT (port-forwarding) rule on a router. |
| `twc_floating_ip` | A floating public IP, optionally bound to a server/LB/DB. |
| `twc_firewall` | A firewall group, linked to servers/DBs/LBs/apps. |
| `twc_firewall_rule` | A single ingress/egress rule inside a firewall group. |
| `twc_dns_rr` | A DNS resource record (A, AAAA, CNAME, MX, TXT). |

Data sources: `twc_vpc` inputs come from the `twc_vpc` resource; `twc_router_preset`,
`twc_dns_zone`, `twc_floating_ip`.

## Load Balancers

| Resource | Purpose |
|---|---|
| `twc_lb` | A managed load balancer (algorithm, health check, backend IPs). |
| `twc_lb_rule` | A balancing rule (ingress protocol/port → backend protocol/port). |

Data source: `twc_lb_preset` (plans; filter by `requests_per_second`).

## Object Storage (S3)

| Resource | Purpose |
|---|---|
| `twc_s3_bucket` | An S3 bucket (hot/cold, private/public, optional static website). |
| `twc_s3_bucket_directory` | A directory (key prefix) inside a bucket. |
| `twc_s3_bucket_file` | An object uploaded into a bucket. |
| `twc_s3_bucket_subdomain` | A custom subdomain for a bucket. |
| `twc_s3_user` | An S3 (IAM-style) access user. |

Data sources: `twc_s3_preset` (plans; `storage_class = hot|cold`),
`twc_s3_configurator`, `twc_s3_bucket`.

## Network Drives & Projects

| Resource | Purpose |
|---|---|
| `twc_network_drive` | A network block storage volume, optionally mounted to a server. |
| `twc_project` | A project used to group resources in the account. |

Data sources: `twc_network_drive_preset`, `twc_network_drive`, `twc_projects`.

## Utility

| Resource | Purpose |
|---|---|
| `twc_random_string` | Generates a random string (useful for names/passwords). |

---

See also: **Common Recipes** for end-to-end configurations, and
**Conventions, Constraints and Gotchas** for locations, presets vs configurators,
create-only fields, and mutually exclusive arguments.
