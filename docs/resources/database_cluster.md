---
page_title: "twc_database_cluster Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed database cluster and provides actual information about its status
---

# twc_database_cluster (Resource)

Resource for describing needed database cluster and provides actual information about its status

## Примеры использования

```terraform
# Select any preset from location = "ru-1", 8 Gb disk space with price between 100 and 500 RUB for MySQL
data "twc_database_preset" "example-db-preset" {
  location = "ru-1"

  type = "mysql"

  disk = 8 * 1024

  price_filter {
    from = 100
    to   = 500
  }
}

# Create example cluster of MySQL type with auto_increment_increment parameter override
resource "twc_database_cluster" "example-mysql-8" {
  name = "example_mysql_8"

  type = "mysql"

  config_parameters = {
    auto_increment_increment = 3
  }

  preset_id = data.twc_database_preset.example-db-preset.id
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `name` (String) Name for database cluster
- `preset_id` (Number) Preset ID for database cluster
- `type` (String) Type of database cluster

### Optional

- `availability_zone` (String) Availability zone for database cluster
- `config_parameters` (Map of String) Configuration parameters for database cluster
- `description` (String) Description for project
- `hash_type` (String) Hash type for database
- `is_external_ip` (Boolean) Flag that shows allowability database only by external IP address
- `network` (Block List, Max: 1) Network for database cluster (see [below for nested schema](#nestedblock--network))
- `project_id` (Number) Project ID for managed resource
- `replications` (Number) Number of replication instances

### Read-Only

- `disk_stats` (List of Object) Information about database disk stats (see [below for nested schema](#nestedatt--disk_stats))
- `id` (String) The ID of this resource.
- `location` (String) Location for the server (`ru-1`, `ru-2`, `pl-1`, `kz-1`, etc.)
- `networks` (List of Object) (see [below for nested schema](#nestedatt--networks))
- `parameters` (Map of String) Configuration parameters for database cluster
- `port` (Number) Listening port for incoming connections
- `status` (String) Current status of database cluster (`started`, `starting`, `stopped`, `no_paid`)

<a id="nestedblock--network"></a>
### Nested Schema for `network`

Required:

- `id` (String) Network ID


<a id="nestedatt--disk_stats"></a>
### Nested Schema for `disk_stats`

Read-Only:

- `size` (Number)
- `used` (Number)


<a id="nestedatt--networks"></a>
### Nested Schema for `networks`

Read-Only:

- `ips` (List of Object) (see [below for nested schema](#nestedobjatt--networks--ips))
- `type` (String)

<a id="nestedobjatt--networks--ips"></a>
### Nested Schema for `networks.ips`

Read-Only:

- `ip` (String)
- `type` (String)

