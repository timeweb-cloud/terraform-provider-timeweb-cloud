---
page_title: "twc_database_cluster Data Source - Timeweb Cloud"
subcategory: ""
description: |-
  Data source that provides bunch of useful parameters for filtering and suitable database cluster selection. All parameters are filters, so you can use only needed. Available DB types may be retrieved from API https://api.timeweb.cloud/api/v1/database-types
---

# twc_database_cluster (Data Source)

Data source that provides bunch of useful parameters for filtering and suitable database cluster selection. All parameters are filters, so you can use only needed. Available DB types [may be retrieved from API](https://api.timeweb.cloud/api/v1/database-types)



<!-- schema generated by tfplugindocs -->
## Schema

### Optional

- `availability_zone` (String) Availability zone for database cluster
- `id` (String) Database cluster ID
- `location` (String) Location where database cluster was created
- `name` (String) Name of specified database cluster
- `type` (String) Type of specified database cluster

### Read-Only

- `description` (String) Description of specified database cluster
- `disk_stats` (List of Object) Information about database disk stats (see [below for nested schema](#nestedatt--disk_stats))
- `is_external_ip` (Boolean) Flag that shows allowability database only by external IP address
- `networks` (List of Object) (see [below for nested schema](#nestedatt--networks))
- `parameters` (Map of String) Configuration parameters for database cluster
- `port` (Number) Listening port for incoming connections
- `status` (String) Current status of database cluster (`started`, `starting`, `stopped`, `no_paid`)

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

