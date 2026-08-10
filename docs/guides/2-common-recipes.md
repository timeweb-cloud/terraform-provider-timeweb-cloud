---
page_title: "Common Recipes"
subcategory: "Guides"
description: |-
  Copy-paste, end-to-end Terraform configurations for the most common Timeweb Cloud scenarios: a web server, a managed database with backups, a Kubernetes cluster, a load-balanced backend, a private network, and an S3 static website.
---

# Common Recipes

Each recipe below is a complete, self-contained configuration. Copy one, adjust
names, locations and presets, and run `terraform apply`. All of them assume the
provider is configured and `TWC_TOKEN` is set (see the provider index page).

The per-resource pages contain the full argument reference; these recipes show how
the resources are **wired together**, which is where most configurations go wrong.

## 1. A web server: SSH key + firewall + floating IP + DNS

Creates a server reachable over SSH, protected by a firewall that allows only
SSH/HTTP/HTTPS, with a floating IP and an `A` record pointing at it.

```terraform
data "twc_os" "ubuntu" {
  name    = "ubuntu"
  version = "22.04"
}

data "twc_presets" "small" {
  price_filter {
    from = 300
    to   = 400
  }
}

resource "twc_ssh_key" "deploy" {
  name = "deploy-key"
  body = file("~/.ssh/id_ed25519.pub")
}

resource "twc_server" "web" {
  name  = "web-1"
  os_id = data.twc_os.ubuntu.id

  preset_id = data.twc_presets.small.id

  ssh_keys_ids              = [twc_ssh_key.deploy.id]
  is_root_password_required = false # SSH keys are set, so no root password is needed
}

resource "twc_firewall" "web" {
  name = "web-firewall"

  link {
    id   = twc_server.web.id
    type = "server"
  }
}

resource "twc_firewall_rule" "ssh" {
  firewall_id = twc_firewall.web.id
  direction   = "ingress"
  protocol    = "tcp"
  port        = "22"
  cidr        = "0.0.0.0/0"
}

resource "twc_firewall_rule" "http" {
  firewall_id = twc_firewall.web.id
  direction   = "ingress"
  protocol    = "tcp"
  port        = "80"
  cidr        = "0.0.0.0/0"
}

resource "twc_firewall_rule" "https" {
  firewall_id = twc_firewall.web.id
  direction   = "ingress"
  protocol    = "tcp"
  port        = "443"
  cidr        = "0.0.0.0/0"
}

# A floating IP bound to the server (survives server re-creation)
resource "twc_floating_ip" "web" {
  availability_zone = "spb-3"

  resource {
    type = "server"
    id   = twc_server.web.id
  }
}

# DNS: an A record for app.example.com -> the floating IP.
# The DNS zone (example.com) must already exist in your account.
data "twc_dns_zone" "main" {
  name = "example.com"
}

resource "twc_dns_rr" "web" {
  zone_id = data.twc_dns_zone.main.id
  name    = "app" # host label relative to the zone; "@" for the zone root
  type    = "A"   # A | AAAA | CNAME | MX | TXT
  value   = twc_floating_ip.web.ip
}
```

## 2. A managed database with a user and automatic backups

```terraform
data "twc_database_preset" "pg" {
  location = "ru-1"
  type     = "postgres"
  disk     = 8 * 1024
}

resource "twc_database_cluster" "main" {
  name = "app_postgres"
  type = "postgres"

  preset_id = data.twc_database_preset.pg.id

  config_parameters = {
    max_connections = 100
  }

  is_secure_connection_enabled = true # SSL
}

resource "twc_database_instance" "app" {
  cluster_id = twc_database_cluster.main.id
  name       = "app_db"
}

resource "twc_database_user" "app" {
  cluster_id = twc_database_cluster.main.id
  login      = "app_user"
  password   = "ChangeMe-Str0ng!"

  # A user must specify exactly one of `privileges` (all databases) or
  # `instance` (per-database). Here we scope it to the instance created above.
  instance {
    instance_id = twc_database_instance.app.id
    privileges  = ["SELECT", "INSERT", "UPDATE", "DELETE"]
  }
}

resource "twc_database_backup_schedule" "daily" {
  cluster_id        = twc_database_cluster.main.id
  enabled           = true
  copy_count        = 7
  interval          = "day"
  creation_start_at = "2026-01-01T00:00:00"
}
```

> The preset is bound to both `type` and `location`. If you change the database
> `type`, pick a `twc_database_preset` for that same type.

## 3. A Kubernetes cluster with a worker node group and an add-on

```terraform
# Master preset (control plane)
data "twc_k8s_preset" "master" {
  cpu  = 4
  type = "master"
}

# Worker preset (nodes)
data "twc_k8s_preset" "worker" {
  cpu  = 2
  type = "worker"
}

resource "twc_k8s_cluster" "main" {
  name              = "app-cluster"
  high_availability = false
  version           = "v1.31.0"
  network_driver    = "flannel"
  ingress           = true

  preset_id = data.twc_k8s_preset.master.id
}

resource "twc_k8s_node_group" "workers" {
  cluster_id = twc_k8s_cluster.main.id
  name       = "workers"
  preset_id  = data.twc_k8s_preset.worker.id
  node_count = 2

  is_autohealing = true
}

resource "twc_k8s_addon" "capsule" {
  cluster_id  = twc_k8s_cluster.main.id
  type        = "capsule"
  version     = "v0.7.2"
  config_type = "basic"
}
```

## 4. A load balancer in front of two servers

```terraform
data "twc_os" "ubuntu" {
  name    = "ubuntu"
  version = "22.04"
}

data "twc_presets" "small" {
  price_filter {
    from = 300
    to   = 400
  }
}

data "twc_lb_preset" "lb" {
  requests_per_second = "10K"
}

resource "twc_server" "backend" {
  count     = 2
  name      = "backend-${count.index}"
  os_id     = data.twc_os.ubuntu.id
  preset_id = data.twc_presets.small.id
}

resource "twc_lb" "app" {
  name = "app-lb"
  algo = "roundrobin" # roundrobin | leastconn

  health_check {
    proto   = "http"
    port    = 80
    path    = "/health"
    inter   = 10
    timeout = 5
    fall    = 3
    rise    = 2
  }

  # Backend server IPs
  ips = twc_server.backend[*].main_ipv4

  preset_id = data.twc_lb_preset.lb.id
}

resource "twc_lb_rule" "http" {
  lb_id          = twc_lb.app.id
  balancer_proto = "http" # http | http2 | https | tcp
  balancer_port  = 80
  server_proto   = "http"
  server_port    = 80
}
```

## 5. A private network (VPC) with a server, a database and a load balancer

```terraform
resource "twc_vpc" "main" {
  name      = "app-vpc"
  subnet_v4 = "192.168.0.0/24"
  location  = "ru-1" # ru-1 | ru-3 | de-1 | nl-1
}

data "twc_os" "ubuntu" {
  name    = "ubuntu"
  version = "22.04"
}

data "twc_presets" "small" {
  price_filter {
    from = 300
    to   = 400
  }
}

resource "twc_server" "app" {
  name      = "app"
  os_id     = data.twc_os.ubuntu.id
  preset_id = data.twc_presets.small.id

  local_network {
    id = twc_vpc.main.id
    # ip = "192.168.0.10" # optional fixed address
  }
}

data "twc_database_preset" "redis" {
  location = "ru-1"
  type     = "redis"
  disk     = 8 * 1024
}

resource "twc_database_cluster" "cache" {
  name      = "app_cache"
  type      = "redis"
  preset_id = data.twc_database_preset.redis.id

  network {
    id = twc_vpc.main.id
  }
}

data "twc_lb_preset" "lb" {
  requests_per_second = "10K"
}

resource "twc_lb" "app" {
  name      = "app-lb"
  algo      = "roundrobin"
  preset_id = data.twc_lb_preset.lb.id

  local_network {
    id = twc_vpc.main.id
    ip = "192.168.0.20"
  }
}
```

> **Order matters:** the VPC must exist before resources join it. Do not create a
> VPC and attach a resource to it via a router `networks` block in the *same* apply —
> pre-create the VPC (a separate apply, or rely on Terraform's dependency ordering
> through `twc_vpc.main.id` references, which the examples above use).

## 6. An S3 bucket serving a static website

```terraform
data "twc_s3_preset" "hot" {
  location      = "ru-1" # in production S3 is available only in ru-1
  storage_class = "hot"
  disk          = 10 * 1024
}

resource "twc_s3_bucket" "site" {
  name      = "my-static-site"
  type      = "public" # private | public | photos
  preset_id = data.twc_s3_preset.hot.id

  website_config {
    enabled    = true
    index_page = "index.html"

    error_pages {
      code     = 404
      document = "404.html"
    }
  }
}

resource "twc_s3_bucket_file" "index" {
  bucket_id = twc_s3_bucket.site.id
  path      = "index.html"
  file      = filebase64("./site/index.html") # base64-encoded file contents
}
```

---

See **Conventions, Constraints and Gotchas** for the rules that keep these
configurations valid (locations, presets vs configurators, create-only fields,
mutually exclusive arguments).
