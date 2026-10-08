---
page_title: "Provider: Utho"
description: |-
  The Utho provider is used to manage infrastructure on Utho Cloud, including cloud instances, Kubernetes, managed databases, networking, storage, and more.
---

# Utho Provider

The Utho provider is used to configure infrastructure on [Utho Cloud](https://utho.com) using the Utho API. With it you can define cloud instances, managed Kubernetes clusters, managed databases, VPCs, load balancers, DNS, object storage, container registries, monitoring, and IAM as code, and manage their full lifecycle with Terraform.

Documentation for the resources and data sources supported by the Utho provider can be found in the navigation to the left, grouped by service.

To learn the basics of Terraform using this provider, follow the [Getting Started](guides/getting-started) guide. For end-to-end examples, see the tutorials under **Guides**, such as [Deploying a Production Web Stack](guides/production-web-stack) and [Private Networking with VPC and NAT](guides/private-networking).

## Example Usage

```hcl
terraform {
  required_providers {
    utho = {
      source  = "dharmajputho/utho-dev"
      version = "~> 0.2"
    }
  }
}

variable "utho_api_key" {
  type      = string
  sensitive = true
}

# Configure the Utho provider
provider "utho" {
  api_key = var.utho_api_key
}

# Create an SSH key
resource "utho_ssh_key" "deploy" {
  name   = "deploy-key"
  sshkey = file("~/.ssh/id_ed25519.pub")
}

# Create a cloud instance
resource "utho_cloud" "web" {
  hostname        = "web-01.mhc"
  dcslug          = "inmumbaizone2"
  planid          = "10308"
  billingcycle    = "hourly"
  auth            = "option2"
  sshkeys         = utho_ssh_key.deploy.id
  image           = "ubuntu-22.04-x86_64"
  enable_publicip = "true"
}

output "web_ip" {
  value = utho_cloud.web.ip
}
```

## Authentication and Configuration

The Utho provider authenticates with a Utho API key. You can create one in the [Utho Console](https://console.utho.com) under **Settings → API Tokens**.

The key is passed to the provider with the `api_key` argument, which is required. Always supply it through a sensitive input variable rather than writing the key into your configuration.

!> **Warning:** Hard-coded credentials are not recommended in any Terraform configuration and risk secret leakage should the file ever be committed to a public version control system.

```hcl
variable "utho_api_key" {
  type      = string
  sensitive = true
}

provider "utho" {
  api_key = var.utho_api_key
}
```

Set the variable with the `TF_VAR_utho_api_key` environment variable, so the key never appears in your files:

```bash
export TF_VAR_utho_api_key="your-api-key"
terraform plan
```

For more detail, including secrets managers, multiple accounts, and authenticating from CI/CD pipelines and HCP Terraform, see the [Authenticating with an API Key](guides/authentication-api-key) and [Authenticating in CI/CD and HCP Terraform](guides/authentication-ci-cd) guides.

## Argument Reference

The following arguments are supported in the `utho` provider block:

* `api_key` - (Required) Utho API key. Supply it through a sensitive variable, as shown above.
* `base_url` - (Optional) Override the Utho API base URL. Defaults to `https://api.utho.com/v2`. This is only needed for testing against a non-production API endpoint.

## Data Centers

Most Utho resources take a `dcslug` argument that selects the data center they are created in.

| Slug | Location |
|------|----------|
| `innoida` | Delhi (Noida), India |
| `inmumbaizone2` | Mumbai, India |
| `inbangalore` | Bangalore, India |
| `defra1` | Frankfurt, Germany |
| `uslosangeles` | Los Angeles, United States |

Not every service, plan, or feature is available in every data center. Use the [`utho_cloud_dczones`](data-sources/cloud_dczones) data source to look up current availability, and see the [Data Centers, Plans, and Images](guides/data-centers-plans-images) guide for discovering valid `dcslug`, `planid`, and `image` values without hard-coding them.

## Getting Help

* Browse the [Guides](guides/getting-started) for tutorials and common workflows.
* See [Debugging and Troubleshooting](guides/troubleshooting) for common errors and how to enable debug logging.
* Report bugs or request features on the provider's [GitHub repository](https://github.com/dharmajputho/terraform-provider-utho-dev/issues).
