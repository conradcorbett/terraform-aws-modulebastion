# terraform-aws-modulebastion

Demo to show using the PMR in TFC. This module is already published in TFC, module is located at https://github.com/conradcorbett/terraform-aws-modulebastion. To use the module, follow the steps below.

```shell
mkdir demo && cd demo
export TF_CLOUD_ORGANIZATION=SeeSquared
```

### Create `main.tf`

```shell
cat <<EOF >>main.tf

provider "aws" {
  region = var.aws_region
}

module "modulebastion" {
  source  = "app.terraform.io/SeeSquared/modulebastion/aws"
  version = "1.0.0"
  bastion_instance_type = var.bastion_instance_type
}
EOF
```

### Create `variables.tf`

```shell
cat <<EOF >>variables.tf
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "us-east-2"
}

variable "bastion_instance_type" {
  description = "EC2 instance type to use for bastion instance"
  type        = string
  default     = "t2.small"
}
EOF
```

### Create `terraform.tf`

```shell
cat <<EOF >>terraform.tf
terraform {
  cloud {
    workspaces {
      name = "demo-PMR"
    }
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.10.0"
    }
  }
}
EOF
```

### Bonus demo with Sentinel policies
Enforce a sentinel policy that will only let you deploy from modules in the PMR. Under Policy Sets, apply the "sentinel" policy set to all workspaces in the default project.
The policies can be viewed at https://github.com/conradcorbett/sentinel

### Add unapproved module to `main.tf`

```shell
cat <<EOF >>main.tf

data "aws_availability_zones" "available" {
  state = "available"

  filter {
    name   = "zone-type"
    values = ["availability-zone"]
  }
}

module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"

  cidr = "10.0.0.0/16"

  azs             = data.aws_availability_zones.available.names
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.10.0/24", "10.0.11.0/24"]

  enable_nat_gateway   = true
  enable_vpn_gateway   = false
  enable_dns_hostnames = true
}
EOF
```