# terraform-aws-modulebastion

Demo to show using the PMR in TFC. This module is already published in TFC, module is located at https://github.com/conradcorbett/terraform-aws-modulebastion. To use the module, follow the steps below.

```shell
mkdir demo && cd demo
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
cat <<EOF >>variables.tf
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