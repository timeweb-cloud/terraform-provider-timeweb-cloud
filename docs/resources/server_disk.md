---
page_title: "twc_server_disk Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed additional disk for server and provides actual information about its status
---

# twc_server_disk (Resource)

Resource for describing needed additional disk for server and provides actual information about its status

## Примеры использования

```terraform
resource "twc_server_disk" "example" {
  source_server_id = twc_server.example-server.id

  size = 1024 * 10
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `size` (Number) Disk size for created disk in MB, minimal size is `5120`, step is `5120`
- `source_server_id` (Number) Server ID for which disk should be created

### Read-Only

- `id` (String) The ID of this resource.
- `is_mounted` (Boolean) Flag that shows current mount status of disk
- `is_system` (Boolean) Flag that shows disk is system or not (always `false` for now)
- `status` (String) Current status of disk
- `system_name` (String) The name of the disk it was mounted on
- `type` (String) Disk type (`ssd`, `nvme`, `hdd`)
- `used` (Number) Used disk space in MB

