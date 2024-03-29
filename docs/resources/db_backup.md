---
page_title: "twc_db_backup Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed DB backup and provides actual information about its status
---

# twc_db_backup (Resource)

Resource for describing needed DB backup and provides actual information about its status

## Примеры использования

```terraform
# Setup example database for backup schedule configuration
data "twc_db_preset" "example-db-postgres-preset" {
  location = "ru-1"

  type = "postgres"

  price_filter {
    from = 100
    to = 200
  }
}

resource "twc_db_postgres" "example-postgres" {
  name = "example_postgres"

  login = "example_login"
  password = "example_password"

  preset_id = data.twc_db_preset.example-db-postgres-preset.id

  autovacuum_analyze_scale_factor = 0.001
  bgwriter_delay = 101
  bgwriter_lru_maxpages = 102
  deadlock_timeout = 103
  gin_pending_list_limit = 104
  idle_in_transaction_session_timeout = 115
  idle_session_timeout = 106
  join_collapse_limit = 107
  lock_timeout = 108
  max_prepared_transactions = 109
  max_connections = 110
  shared_buffers = 111
  wal_buffers = 112
  temp_buffers = 113
  work_mem = 114

  is_external_ip = true
}

# Create backup for example database
resource "twc_db_backup" "example-db-backup" {
  source_db_id = twc_db_postgres.example-postgres.id

  comment = "Example database"
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `source_db_id` (Number) DB ID for which backup should be created

### Optional

- `comment` (String) Comment for backup

### Read-Only

- `created_at` (String) Date when backup was created
- `id` (String) The ID of this resource.
- `name` (String) Name of backup
- `size` (Number) Backups size
- `status` (String) Current status of backup
- `type` (String) Backup type (`manual` or `auto`)

