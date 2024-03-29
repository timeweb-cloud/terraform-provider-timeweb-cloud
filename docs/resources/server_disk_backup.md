---
page_title: "twc_server_disk_backup Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed disk backup and provides actual information about its status
---

# twc_server_disk_backup (Resource)

Resource for describing needed disk backup and provides actual information about its status

## Примеры использования

```terraform
data "twc_os" "example-os" {
  name = "ubuntu"
  version = "22.04"
}

data "twc_presets" "example-preset" {
  price_filter {
    from = 300
    to = 400
  }
}

resource "twc_server" "example-server" {
  name = "Example server with preset"
  os_id = data.twc_os.example-os.id

  preset_id = data.twc_presets.example-preset.id
}

resource "twc_server_disk" "example-additional-disk" {
  source_server_id = twc_server.example-server.id

  size = 1024 * 10
}

### Backup from main server disk
resource "twc_server_disk_backup" "example" {
  source_server_id = twc_server.example-server.id
  source_server_disk_id = twc_server.example-server.disks[0].id

  comment = "example with main disk"
}

### Backup from additional disk resource
resource "twc_server_disk_backup" "example-with-additional-disk" {
  source_server_id = twc_server_disk.example-disk.source_server_id
  source_server_disk_id = twc_server_disk.example-additional-disk.id

  comment = "example with additional disk"
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `source_server_disk_id` (Number) Disk ID for which backup should be created
- `source_server_id` (Number) Server ID for which disk backups should be created

### Optional

- `comment` (String) Comment for backup

### Read-Only

- `created_at` (String) Date when backup was created
- `id` (String) The ID of this resource.
- `name` (String) Name of backup
- `size` (Number) Backups size
- `status` (String) Current status of backup
- `type` (String) Backup type (always `manual` for now)

## Import

Import is supported using the following syntax:

```shell
# Server disk backup can be imported by specifying the numeric identifier in format SERVER-ID/SERVER-DISK-ID/SERVER-DISK-BACKUP-ID (all parameters from URL)
terraform import twc_server_disk_backup.example 42/13/5
```