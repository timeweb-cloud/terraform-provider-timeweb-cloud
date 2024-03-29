---
page_title: "twc_database_instance Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed database instance and provides actual information about its status
---

# twc_database_instance (Resource)

Resource for describing needed database instance and provides actual information about its status

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

# Create example cluster of MySQL
resource "twc_database_cluster" "example-mysql-8" {
  name = "example_mysql_8"

  type = "mysql"

  preset_id = data.twc_database_preset.example-db-preset.id
}

# Create example instance in previously created cluster
resource "twc_database_instance" "example-mysql-8-instance" {
  cluster_id = twc_database_cluster.example-mysql-8.id

  name = "example"
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `cluster_id` (Number) Database cluster ID for which backup instance be created
- `name` (String) Name for database instance

### Optional

- `description` (String) Description for project

### Read-Only

- `id` (String) The ID of this resource.

