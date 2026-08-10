---
page_title: "Conventions, Constraints and Gotchas"
subcategory: "Guides"
description: |-
  The rules that keep a Timeweb Cloud configuration valid: authentication, locations and availability zones, presets vs configurators, allowed enum values, create-only (ForceNew) arguments, mutually exclusive arguments, and common pitfalls.
---

# Conventions, Constraints and Gotchas

This page collects the cross-cutting rules that are easy to miss. Reading it once
will prevent most `terraform plan`/`apply` failures.

## Authentication

The provider needs an API token from the Timeweb Cloud
[control panel](https://timeweb.cloud/my/api-keys). Provide it either via the
`TWC_TOKEN` environment variable (preferred) or the `token` provider argument:

```terraform
provider "twc" {
  token = "eyJhbGci..." # or leave empty and export TWC_TOKEN
}
```

> The token must have **deletion confirmation via Telegram disabled**, otherwise
> `terraform destroy` on servers will hang waiting for manual confirmation.

## Locations vs availability zones

These are two different things and are used in different arguments:

- **Location** — a data-center region, e.g. `ru-1`, `ru-2`, `de-1`, `kz-1`,
  `nl-1`. It is chosen through a catalog data source (`twc_configurator`,
  `twc_presets`, `twc_database_preset`, ...) or set directly where a resource is
  region-scoped. `twc_vpc.location` accepts `ru-1`, `ru-3`, `de-1`, `nl-1`.
- **Availability zone** — a specific zone inside a region: `spb-1`, `spb-3`,
  `spb-4`, `msk-1`, `nsk-1`, `ams-1`, `fra-1`, `ala-1`. Used by
  `twc_server.availability_zone` and required by `twc_floating_ip.availability_zone`.

Each location maps to an availability zone as follows:

| Location | Availability zone |
|---|---|
| `ru-1` | `spb-3` |
| `ru-2` | `nsk-1` |
| `ru-3` | `msk-1` |
| `nl-1` | `ams-1` |
| `de-1` | `fra-1` |
| `kz-1` | `ala-1` |

Two rules follow from this:

1. **Resources that talk to each other must share a location/zone.** A floating IP
   in `spb-3` can only bind a server created in `spb-3`; a VPC in `ru-1` can only
   hold resources in `ru-1`.
2. Once a server is created, new presets/configurators for it can only be chosen
   from the **same location**.

## Presets vs configurators (sizing)

Most compute/storage products can be sized in one of two ways — **pick one**:

- **`preset_id`** — a fixed, ready-made plan selected from a `*_preset` data
  source (`twc_presets`, `twc_database_preset`, `twc_k8s_preset`, `twc_lb_preset`,
  `twc_s3_preset`, `twc_network_drive_preset`, `twc_router_preset`). Simplest and
  most common.
- **`configuration { ... }`** — a flexible custom size (CPU/RAM/disk) bound to a
  `twc_configurator` / `twc_s3_configurator`, subject to the configurator's
  min/max/step limits.

For `twc_server` and `twc_s3_bucket`, `preset_id` and `configuration` are mutually
exclusive. In production, **S3 supports only presets** — configurators are usually
unavailable, so use `twc_s3_preset` for buckets.

Database, k8s, LB, network-drive and router presets are **bound to a type and/or
location**: pick a `twc_database_preset` whose `type` matches the cluster `type`,
a `twc_k8s_preset` with `type = "master"` for the cluster and `type = "worker"`
for node groups, etc.

## Allowed values (enums)

The values below are enforced by the provider. Using anything else fails at
`plan` time.

| Resource / Data source | Argument | Allowed values |
|---|---|---|
| `twc_server` | `local_network.mode` | `dnat_and_snat`, `snat`, `no_nat` |
| `twc_server_ip` | `type` | `ipv4`, `ipv6` |
| `twc_database_cluster` | `hash_type` (MySQL) | `caching_sha2`, `mysql_native` |
| `twc_database_backup_schedule` | `interval` | `day`, `week`, `month` |
| `twc_database_backup_schedule` | `day_of_week` | `1`..`7` (1 = Monday) |
| `twc_server_disk_backup_schedule` | `interval` | `day`, `week`, `month` |
| `twc_dns_rr` | `type` | `A`, `AAAA`, `CNAME`, `MX`, `TXT` |
| `twc_lb` | `algo` | `roundrobin`, `leastconn` |
| `twc_lb` / `twc_lb_rule` | `*_proto`, `health_check.proto` | `http`, `http2`, `https`, `tcp` |
| `twc_firewall` | `link.type` | `server`, `dbaas`, `balancer`, `app` |
| `twc_firewall_rule` | `direction` | `ingress`, `egress` |
| `twc_firewall_rule` | `protocol` | `tcp`, `udp`, `icmp`, `tcp6`, `udp6`, `icmp6` |
| `twc_router_dnat_rule` | `protocol` | `tcp`, `udp`, `tcp_udp` |
| `twc_k8s_cluster` | `maintenance_slot.type` | `any_time`, `fixed_time` |
| `twc_s3_bucket` | `type` | `private`, `public`, `photos` |
| `twc_s3_user` | `status` | `active`, `inactive` |
| `twc_vpc` | `location` | `ru-1`, `ru-3`, `de-1`, `nl-1` |
| `twc_configurator` | `preset_type` | `premium`, `standard`, `gpu`, `dedicated_cpu` |
| `twc_presets` | `preset_type` | `premium`, `standard`, `high_cpu` |
| `twc_k8s_configurator` / `twc_k8s_preset` | `type` | `master`, `worker` |
| `twc_k8s_configurator` | `preset_type` | `premium`, `dedicated_cpu`, `gpu` |
| `twc_network_drive_preset` | `type` | `nvme`, `hdd` |
| `twc_s3_configurator` / `twc_s3_preset` | `storage_class` | `hot`, `cold` |

`twc_database_cluster.type` (and `twc_database_preset.type`) is **not** a fixed
enum in the provider — the set of engines is validated against the API at runtime.
Accepted families include `mysql`, `mysql5`, `postgres`, `redis`, `redis7`,
`valkey`, `mongodb`, `clickhouse`, `opensearch`, `kafka`, `rabbitmq`. The full,
current list is `GET https://api.timeweb.cloud/api/v1/database-types`. Always
select a preset for the exact engine with `twc_database_preset` (its `type` must
match the cluster's `type`) rather than hard-coding IDs.

## Create-only arguments (ForceNew)

Some arguments cannot be changed in place — editing them forces Terraform to
**destroy and re-create** the resource. Plan accordingly (especially for stateful
resources like databases and servers). Notable create-only arguments:

- `twc_server`: `source_server_id`, `availability_zone`, `is_ddos_guard`, and the
  whole `local_network { }` block.
- `twc_database_cluster`: `type`, `hash_type`, `availability_zone`, and the whole
  `admin { }` block.
- `twc_database_instance`: `name` (the API forbids renaming a database).
- `twc_k8s_cluster`: `high_availability`, `network_driver`, `ingress`.
- `twc_k8s_node_group`: `cluster_id`, `name`.
- `twc_router_dnat_rule`: all fields (`protocol`, `local_ip`, `public_ip`, ports).

> The exact per-resource list is shown on each resource page — arguments marked
> *"Requires replacement"* in the schema are create-only.

## Mutually exclusive arguments

- `twc_server`: exactly one origin — `os_id`, `image_id`, `software_id`, or
  `source_server_id` (cloning). For an OS install, `os_id` is required; the others
  are alternatives.
- `twc_server` / `twc_s3_bucket`: `preset_id` **or** `configuration { }`, not both.
- `twc_database_user`: exactly one of `privileges` (grant on all databases, with
  `for_all = true`) **or** one/more `instance { }` blocks (per-database grants).
- `twc_k8s_addon`: the `config { }` block is only valid for the `csi_s3` add-on.

## Common pitfalls

- **Pre-create the VPC.** Attaching a resource to a network in the *same apply*
  that creates the VPC (particularly through a router `networks` block) can be
  silently skipped because the VPC is not ready. Let the VPC be created first
  (reference `twc_vpc.<name>.id` so Terraform orders it, or split the apply).
- **`admin` on a database is create-only and write-only.** Engines like
  `clickhouse` require an `admin { }` at creation. The API never returns the
  password, so the block cannot be updated later — only set on create.
- **Floating IP needs a zone.** `twc_floating_ip.availability_zone` is required
  and must match the zone of the resource you bind it to.
- **Root password vs SSH keys.** If you set `ssh_keys_ids`, you can set
  `is_root_password_required = false`. If you set neither, the server has no way in.
- **S3 in production is `ru-1` only** and works via presets.
- **Scaling a k8s node group down** is an asynchronous operation; reducing
  `node_count` may take a while to settle — reduce gradually and watch the cluster.
- **DNS `MX` value format** is `"<priority> <host>"`, e.g. `"10 mx.example.com"`;
  host names are written without a trailing dot.

---

See also: the **Resource and Data Source Map** for what exists and how IDs flow,
and **Common Recipes** for complete configurations.
