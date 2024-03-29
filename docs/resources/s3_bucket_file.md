---
page_title: "twc_s3_bucket_file Resource - Timeweb Cloud"
subcategory: ""
description: |-
  Resource for describing needed file in S3 bucket.
  This resource support nested directories, but be carefully when parent directories are managed by other resource or managed directly from UI -
  this may lead to errors and requires manually removing from state
---

# twc_s3_bucket_file (Resource)

Resource for describing needed file in S3 bucket.
This resource support nested directories, but be carefully when parent directories are managed by other resource or managed directly from UI -
this may lead to errors and requires manually removing from state

## Примеры использования

```terraform
data "twc_s3_preset" "example-s3-preset" {
  location = "ru-1"

  disk = 10 * 1024

  price_filter {
    from = 50
    to = 100
  }
}

resource "twc_s3_bucket" "example-s3-bucket" {
  name = "example-s3-bucket"
  type = "private"
  preset_id = data.twc_s3_preset.example-s3-preset.id
}

# Create some_image.png in previously created bucket
resource "twc_s3_bucket_file" "example-s3-bucket-file" {
  bucket_id = twc_s3_bucket.example-s3-bucket.id

  path = "some_image.png"

  file = filebase64("./some_image.png")
}


# Create some_directory for next example
resource "twc_s3_bucket_directory" "example-s3-bucket-directory" {
  bucket_id = twc_s3_bucket.example-s3-bucket.id

  name = "some_directory"
}

# Create some_image.png in previously created directory
resource "twc_s3_bucket_file" "example-s3-bucket-file-in-directory" {
  bucket_id = twc_s3_bucket.example-s3-bucket.id

  path = "some_directory/some_image.png"

  file = filebase64("./some_image.png")

  ## Explicit dependencies helps terraform to build correct plan
  depends_on = [twc_s3_bucket_directory.example-s3-bucket-directory]
}
```
<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `bucket_id` (Number) S3 Bucket ID for which file should be created
- `file` (String) File base64-encoded content
- `path` (String) File path in S3 bucket

### Read-Only

- `id` (String) The ID of this resource.

