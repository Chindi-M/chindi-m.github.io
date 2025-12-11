---
layout: post
title: "Terraform for DevOps Engineers: A Practical Guide"
date: 2025-01-08
categories: [devops, infrastructure]
tags: [terraform, infrastructure-as-code, iac, devops, tutorial, beginner-friendly, hands-on, cloud, automation, provisioning]
---

Terraform manages infrastructure as code. It allows you to define servers, networks, databases and other resources using configuration files. Terraform provisions infrastructure across multiple cloud providers consistently and predictably. This guide introduces Terraform's concepts, workflow and commands with practical examples you can follow along.

---

## 1. What Is Terraform and Why Does It Exist

Before infrastructure as code, engineers created resources manually through web consoles or CLI commands. This approach was slow, error-prone and difficult to replicate. Tracking what existed in production required spreadsheets or manual audits. Coordinating changes across teams created conflicts and inconsistencies.

Terraform solves these problems by treating infrastructure as code. You write configuration files that describe the desired state of your infrastructure. Terraform compares this desired state with the actual state and makes the necessary changes. This approach provides several benefits:

- Infrastructure changes are versioned in Git
- Changes can be reviewed before applying
- Infrastructure is reproducible across environments
- Multiple team members can collaborate safely
- Resources are documented in code rather than wikis

Terraform supports multiple cloud providers including AWS, Azure, Google Cloud and many others. It also manages services like GitHub, Datadog and PagerDuty. This allows you to manage your entire stack with a single tool.

---

## 2. Core Concepts

Terraform uses several key concepts to manage infrastructure.

### Provider

A provider is a plugin that allows Terraform to interact with an API. Providers exist for cloud platforms, SaaS services and other infrastructure tools.

Common providers include:

- AWS
- Azure
- Google Cloud Platform
- Kubernetes
- Docker
- GitHub
- Datadog

### Resource

A resource is a component of your infrastructure. Resources can be virtual machines, storage buckets, databases, networks or any other infrastructure component.

### Module

A module is a collection of resources that are used together. Modules allow you to package and reuse infrastructure configurations.

### State

Terraform stores information about your infrastructure in a state file. The state maps your configuration to real-world resources. Terraform uses state to determine what changes to make.

### Plan

A plan shows what changes Terraform will make before applying them. Plans help you verify changes before modifying infrastructure.

### Variables

Variables allow you to parameterise your configurations. They make configurations reusable across different environments.

### Outputs

Outputs extract information from your infrastructure. They can be used to display values or pass data to other configurations.

---

## 3. Installing Terraform

**Download Terraform:**

Visit [terraform.io/downloads](https://www.terraform.io/downloads) and download the appropriate version for your operating system.

**Install on Linux:**

```bash
wget https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_linux_amd64.zip
unzip terraform_1.7.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

**Install on macOS with Homebrew:**

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

**Install on Windows:**

Download the ZIP file, extract it and add the directory to your PATH.

**Verify installation:**

```bash
terraform version
```

You should see the version number.

---

## 4. Basic Terraform Workflow

Terraform follows a consistent workflow:

1. **Write** configuration files
2. **Initialize** the working directory
3. **Plan** changes
4. **Apply** changes
5. **Destroy** resources when finished

This workflow applies to every Terraform project.

---

## 5. Your First Terraform Configuration

Create a simple configuration that outputs a message.

**Create a directory:**

```bash
mkdir terraform-demo
cd terraform-demo
```

**Create main.tf:**

```hcl
terraform {
  required_version = ">= 1.0"
}

output "hello" {
  value = "Hello from Terraform"
}
```

**Initialize Terraform:**

```bash
terraform init
```

This downloads required providers and prepares the working directory.

**Apply the configuration:**

```bash
terraform apply
```

Terraform shows the planned changes and asks for confirmation. Type `yes` to proceed.

You should see the output message.

---

## 6. Basic Terraform Commands

**Initialize a directory:**

```bash
terraform init
```

**Validate configuration:**

```bash
terraform validate
```

**Format configuration files:**

```bash
terraform fmt
```

**Show execution plan:**

```bash
terraform plan
```

**Apply changes:**

```bash
terraform apply
```

**Apply without confirmation:**

```bash
terraform apply -auto-approve
```

**Destroy all resources:**

```bash
terraform destroy
```

**Show current state:**

```bash
terraform show
```

**List resources in state:**

```bash
terraform state list
```

**Show specific resource:**

```bash
terraform state show <resource-address>
```

**Output values:**

```bash
terraform output
```

---

## 7. Understanding Terraform Configuration

Terraform uses HashiCorp Configuration Language (HCL). Configuration files use the `.tf` extension.

### Basic Syntax

**Blocks:**

```hcl
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

**Arguments:**

```hcl
ami = "ami-0c55b159cbfafe1f0"
```

**Expressions:**

```hcl
instance_type = var.instance_type
```

**Comments:**

```hcl
# Single line comment

/*
Multi-line
comment
*/
```

### File Structure

Common file names in a Terraform project:

- `main.tf`: Primary configuration
- `variables.tf`: Variable definitions
- `outputs.tf`: Output definitions
- `terraform.tfvars`: Variable values
- `versions.tf`: Provider version constraints
- `backend.tf`: Backend configuration

You can split configuration across multiple files. Terraform loads all `.tf` files in a directory.

---

## 8. Working with Providers

Providers must be declared before use.

**AWS provider example:**

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "eu-west-1"
}
```

**Azure provider example:**

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

**Google Cloud provider example:**

```hcl
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 5.0"
    }
  }
}

provider "google" {
  project = "my-project-id"
  region  = "europe-west2"
}
```

**Multiple providers:**

```hcl
provider "aws" {
  alias  = "us_east"
  region = "us-east-1"
}

provider "aws" {
  alias  = "eu_west"
  region = "eu-west-1"
}
```

Use the alias in resources:

```hcl
resource "aws_instance" "us_server" {
  provider = aws.us_east
  ami      = "ami-12345678"
}
```

---

## 9. Creating Resources

Resources are the fundamental building blocks.

**AWS EC2 instance:**

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "WebServer"
  }
}
```

**AWS S3 bucket:**

```hcl
resource "aws_s3_bucket" "data" {
  bucket = "my-unique-bucket-name"
  
  tags = {
    Environment = "Production"
  }
}
```

**Azure virtual machine:**

```hcl
resource "azurerm_linux_virtual_machine" "web" {
  name                = "web-vm"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  size                = "Standard_B1s"
  
  admin_username = "adminuser"
  
  network_interface_ids = [
    azurerm_network_interface.main.id,
  ]
  
  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
  
  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }
}
```

**Google Cloud compute instance:**

```hcl
resource "google_compute_instance" "web" {
  name         = "web-server"
  machine_type = "e2-micro"
  zone         = "europe-west2-a"
  
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  
  network_interface {
    network = "default"
    
    access_config {
      // Ephemeral public IP
    }
  }
}
```

---

## 10. Variables

Variables make configurations reusable and flexible.

**variables.tf:**

```hcl
variable "region" {
  description = "AWS region"
  type        = string
  default     = "eu-west-1"
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "environment" {
  description = "Environment name"
  type        = string
}

variable "availability_zones" {
  description = "List of availability zones"
  type        = list(string)
  default     = ["eu-west-1a", "eu-west-1b"]
}

variable "tags" {
  description = "Resource tags"
  type        = map(string)
  default     = {}
}
```

**Use variables in configuration:**

```hcl
resource "aws_instance" "web" {
  ami               = "ami-0c55b159cbfafe1f0"
  instance_type     = var.instance_type
  availability_zone = var.availability_zones[0]
  
  tags = merge(
    var.tags,
    {
      Name = "web-${var.environment}"
    }
  )
}
```

**Set variable values:**

Option 1: Command line

```bash
terraform apply -var="environment=production"
```

Option 2: terraform.tfvars file

```hcl
environment     = "production"
instance_type   = "t2.small"
region          = "us-east-1"
```

Option 3: Environment variables

```bash
export TF_VAR_environment=production
terraform apply
```

**Variable validation:**

```hcl
variable "environment" {
  type = string
  
  validation {
    condition     = contains(["dev", "staging", "production"], var.environment)
    error_message = "Environment must be dev, staging, or production."
  }
}
```

---

## 11. Outputs

Outputs display information after Terraform applies changes.

**outputs.tf:**

```hcl
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.web.id
}

output "instance_public_ip" {
  description = "Public IP address"
  value       = aws_instance.web.public_ip
}

output "instance_private_ip" {
  description = "Private IP address"
  value       = aws_instance.web.private_ip
}

output "s3_bucket_arn" {
  description = "ARN of the S3 bucket"
  value       = aws_s3_bucket.data.arn
}
```

**View outputs:**

```bash
terraform output
```

**View specific output:**

```bash
terraform output instance_public_ip
```

**Output in JSON format:**

```bash
terraform output -json
```

**Use outputs in scripts:**

```bash
INSTANCE_IP=$(terraform output -raw instance_public_ip)
ssh ubuntu@$INSTANCE_IP
```

---

## 12. Data Sources

Data sources allow Terraform to fetch information about existing resources.

**Fetch latest Amazon Linux AMI:**

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

resource "aws_instance" "web" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t2.micro"
}
```

**Fetch availability zones:**

```hcl
data "aws_availability_zones" "available" {
  state = "available"
}

resource "aws_subnet" "main" {
  count             = 2
  availability_zone = data.aws_availability_zones.available.names[count.index]
  cidr_block        = "10.0.${count.index}.0/24"
  vpc_id            = aws_vpc.main.id
}
```

**Fetch existing VPC:**

```hcl
data "aws_vpc" "default" {
  default = true
}
```

---

## 13. Resource Dependencies

Terraform automatically determines dependencies by analyzing references.

**Implicit dependency:**

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "main" {
  vpc_id     = aws_vpc.main.id  # Implicit dependency
  cidr_block = "10.0.1.0/24"
}
```

Terraform creates the VPC before the subnet.

**Explicit dependency:**

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  depends_on = [aws_iam_role_policy.example]
}
```

Use `depends_on` when Terraform cannot detect the dependency automatically.

---

## 14. Count and For Each

**Count:**

Create multiple similar resources.

```hcl
resource "aws_instance" "web" {
  count         = 3
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "web-${count.index}"
  }
}
```

Reference specific instances:

```hcl
output "first_instance_id" {
  value = aws_instance.web[0].id
}
```

**For each:**

Create resources from a map or set.

```hcl
variable "users" {
  type = set(string)
  default = ["alice", "bob", "charlie"]
}

resource "aws_iam_user" "example" {
  for_each = var.users
  name     = each.value
}
```

With maps:

```hcl
variable "instances" {
  type = map(object({
    instance_type = string
    ami           = string
  }))
  
  default = {
    web = {
      instance_type = "t2.micro"
      ami           = "ami-0c55b159cbfafe1f0"
    }
    db = {
      instance_type = "t2.small"
      ami           = "ami-0c55b159cbfafe1f0"
    }
  }
}

resource "aws_instance" "servers" {
  for_each      = var.instances
  ami           = each.value.ami
  instance_type = each.value.instance_type
  
  tags = {
    Name = each.key
  }
}
```

---

## 15. Conditional Expressions

**Ternary operator:**

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.environment == "production" ? "t2.large" : "t2.micro"
}
```

**Conditional resource creation:**

```hcl
variable "create_instance" {
  type    = bool
  default = true
}

resource "aws_instance" "web" {
  count = var.create_instance ? 1 : 0
  
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

---

## 16. Local Values

Locals allow you to assign names to expressions.

```hcl
locals {
  common_tags = {
    Project     = "MyProject"
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
  
  instance_name = "${var.project}-${var.environment}-web"
  
  availability_zones = slice(data.aws_availability_zones.available.names, 0, 2)
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = merge(
    local.common_tags,
    {
      Name = local.instance_name
    }
  )
}
```

---

## 17. Modules

Modules allow you to organize and reuse configurations.

**Create a module:**

Directory structure:

```
modules/
  webserver/
    main.tf
    variables.tf
    outputs.tf
```

**modules/webserver/main.tf:**

```hcl
resource "aws_instance" "web" {
  ami           = var.ami
  instance_type = var.instance_type
  
  tags = {
    Name = var.name
  }
}
```

**modules/webserver/variables.tf:**

```hcl
variable "ami" {
  description = "AMI ID"
  type        = string
}

variable "instance_type" {
  description = "Instance type"
  type        = string
  default     = "t2.micro"
}

variable "name" {
  description = "Instance name"
  type        = string
}
```

**modules/webserver/outputs.tf:**

```hcl
output "instance_id" {
  value = aws_instance.web.id
}

output "public_ip" {
  value = aws_instance.web.public_ip
}
```

**Use the module:**

```hcl
module "web_server_1" {
  source = "./modules/webserver"
  
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  name          = "web-1"
}

module "web_server_2" {
  source = "./modules/webserver"
  
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.small"
  name          = "web-2"
}

output "web1_ip" {
  value = module.web_server_1.public_ip
}
```

**Public modules:**

Use modules from the Terraform Registry:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"
  
  name = "my-vpc"
  cidr = "10.0.0.0/16"
  
  azs             = ["eu-west-1a", "eu-west-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]
  
  enable_nat_gateway = true
}
```

---

## 18. State Management

Terraform stores state in `terraform.tfstate`. Never edit this file manually.

**View state:**

```bash
terraform show
```

**List resources:**

```bash
terraform state list
```

**Show resource details:**

```bash
terraform state show aws_instance.web
```

**Move resource in state:**

```bash
terraform state mv aws_instance.web aws_instance.main
```

**Remove resource from state:**

```bash
terraform state rm aws_instance.web
```

This removes the resource from state without destroying it.

**Import existing resource:**

```bash
terraform import aws_instance.web i-1234567890abcdef0
```

---

## 19. Remote State

Store state remotely for team collaboration and safety.

**S3 backend:**

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "project/terraform.tfstate"
    region         = "eu-west-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

**Initialize backend:**

```bash
terraform init
```

**Azure backend:**

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-state"
    storage_account_name = "tfstate"
    container_name       = "tfstate"
    key                  = "project.terraform.tfstate"
  }
}
```

**Google Cloud Storage backend:**

```hcl
terraform {
  backend "gcs" {
    bucket = "my-terraform-state"
    prefix = "project"
  }
}
```

**Terraform Cloud backend:**

```hcl
terraform {
  cloud {
    organization = "my-org"
    
    workspaces {
      name = "my-project"
    }
  }
}
```

---

## 20. Workspaces

Workspaces allow you to manage multiple environments with the same configuration.

**List workspaces:**

```bash
terraform workspace list
```

**Create workspace:**

```bash
terraform workspace new staging
```

**Switch workspace:**

```bash
terraform workspace select production
```

**Show current workspace:**

```bash
terraform workspace show
```

**Delete workspace:**

```bash
terraform workspace delete staging
```

**Use workspace in configuration:**

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = terraform.workspace == "production" ? "t2.large" : "t2.micro"
  
  tags = {
    Name        = "web-${terraform.workspace}"
    Environment = terraform.workspace
  }
}
```

---

## 21. Practical Example: Complete AWS Infrastructure

This example creates a VPC, subnets, security groups and EC2 instances.

**main.tf:**

```hcl
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.region
}

# VPC
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = merge(
    local.common_tags,
    {
      Name = "${var.project}-vpc"
    }
  )
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  
  tags = merge(
    local.common_tags,
    {
      Name = "${var.project}-igw"
    }
  )
}

# Public Subnets
resource "aws_subnet" "public" {
  count             = length(var.availability_zones)
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 8, count.index)
  availability_zone = var.availability_zones[count.index]
  
  map_public_ip_on_launch = true
  
  tags = merge(
    local.common_tags,
    {
      Name = "${var.project}-public-${count.index + 1}"
      Type = "public"
    }
  )
}

# Route Table
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }
  
  tags = merge(
    local.common_tags,
    {
      Name = "${var.project}-public-rt"
    }
  )
}

# Route Table Association
resource "aws_route_table_association" "public" {
  count          = length(aws_subnet.public)
  subnet_id      = aws_subnet.public[count.index].id
  route_table_id = aws_route_table.public.id
}

# Security Group
resource "aws_security_group" "web" {
  name        = "${var.project}-web-sg"
  description = "Security group for web servers"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = var.admin_cidr_blocks
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = merge(
    local.common_tags,
    {
      Name = "${var.project}-web-sg"
    }
  )
}

# EC2 Instances
resource "aws_instance" "web" {
  count                  = var.instance_count
  ami                    = data.aws_ami.amazon_linux.id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public[count.index % length(aws_subnet.public)].id
  vpc_security_group_ids = [aws_security_group.web.id]
  
  user_data = <<-EOF
              #!/bin/bash
              yum update -y
              yum install -y httpd
              systemctl start httpd
              systemctl enable httpd
              echo "<h1>Web Server ${count.index + 1}</h1>" > /var/www/html/index.html
              EOF
  
  tags = merge(
    local.common_tags,
    {
      Name = "${var.project}-web-${count.index + 1}"
    }
  )
}

# Data source for AMI
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}
```

**variables.tf:**

```hcl
variable "region" {
  description = "AWS region"
  type        = string
  default     = "eu-west-1"
}

variable "project" {
  description = "Project name"
  type        = string
  default     = "webapp"
}

variable "environment" {
  description = "Environment"
  type        = string
  default     = "dev"
}

variable "vpc_cidr" {
  description = "VPC CIDR block"
  type        = string
  default     = "10.0.0.0/16"
}

variable "availability_zones" {
  description = "Availability zones"
  type        = list(string)
  default     = ["eu-west-1a", "eu-west-1b"]
}

variable "instance_count" {
  description = "Number of instances"
  type        = number
  default     = 2
}

variable "instance_type" {
  description = "Instance type"
  type        = string
  default     = "t2.micro"
}

variable "admin_cidr_blocks" {
  description = "CIDR blocks for admin access"
  type        = list(string)
  default     = ["0.0.0.0/0"]
}

locals {
  common_tags = {
    Project     = var.project
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

**outputs.tf:**

```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.main.id
}

output "public_subnet_ids" {
  description = "Public subnet IDs"
  value       = aws_subnet.public[*].id
}

output "web_server_ips" {
  description = "Web server public IPs"
  value       = aws_instance.web[*].public_ip
}

output "security_group_id" {
  description = "Security group ID"
  value       = aws_security_group.web.id
}
```

**Deploy the infrastructure:**

```bash
terraform init
terraform plan
terraform apply
```

**Access web servers:**

```bash
curl http://$(terraform output -raw web_server_ips | jq -r '.[0]')
```

---

## 22. Terraform with Multiple Environments

**Directory structure:**

```
project/
  modules/
    network/
    compute/
  environments/
    dev/
      main.tf
      variables.tf
      terraform.tfvars
    staging/
      main.tf
      variables.tf
      terraform.tfvars
    production/
      main.tf
      variables.tf
      terraform.tfvars
```

**environments/dev/main.tf:**

```hcl
module "network" {
  source = "../../modules/network"
  
  environment = "dev"
  vpc_cidr    = "10.0.0.0/16"
}

module "compute" {
  source = "../../modules/compute"
  
  environment   = "dev"
  instance_type = "t2.micro"
  vpc_id        = module.network.vpc_id
  subnet_ids    = module.network.public_subnet_ids
}
```

**environments/production/main.tf:**

```hcl
module "network" {
  source = "../../modules/network"
  
  environment = "production"
  vpc_cidr    = "10.1.0.0/16"
}

module "compute" {
  source = "../../modules/compute"
  
  environment   = "production"
  instance_type = "t2.large"
  vpc_id        = module.network.vpc_id
  subnet_ids    = module.network.public_subnet_ids
}
```

---

## 23. Terraform in CI/CD Pipelines

**GitHub Actions example:**

```yaml
name: Terraform

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: 1.7.0
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-1
      
      - name: Terraform Init
        run: terraform init
      
      - name: Terraform Format
        run: terraform fmt -check
      
      - name: Terraform Validate
        run: terraform validate
      
      - name: Terraform Plan
        run: terraform plan -no-color
        continue-on-error: true
      
      - name: Terraform Apply
        if: github.ref == 'refs/heads/main' && github.event_name == 'push'
        run: terraform apply -auto-approve
```

**GitLab CI example:**

```yaml
stages:
  - validate
  - plan
  - apply

variables:
  TF_ROOT: ${CI_PROJECT_DIR}/terraform
  TF_STATE_NAME: ${CI_PROJECT_NAME}

before_script:
  - cd ${TF_ROOT}
  - terraform --version

validate:
  stage: validate
  script:
    - terraform init -backend=false
    - terraform validate
    - terraform fmt -check

plan:
  stage: plan
  script:
    - terraform init
    - terraform plan -out=plan.tfplan
  artifacts:
    paths:
      - ${TF_ROOT}/plan.tfplan
    expire_in: 1 week

apply:
  stage: apply
  script:
    - terraform init
    - terraform apply plan.tfplan
  dependencies:
    - plan
  only:
    - main
  when: manual
```

**Jenkins pipeline example:**

```groovy
pipeline {
    agent any
    
    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'eu-west-1'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
        
        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }
        
        stage('Terraform Apply') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Apply Terraform changes?', ok: 'Apply'
                sh 'terraform apply tfplan'
            }
        }
    }
}
```

---

## 24. Sensitive Data Management

**Mark outputs as sensitive:**

```hcl
output "database_password" {
  description = "Database password"
  value       = aws_db_instance.main.password
  sensitive   = true
}
```

**Mark variables as sensitive:**

```hcl
variable "database_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```

**Use AWS Secrets Manager:**

```hcl
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = "database/password"
}

resource "aws_db_instance" "main" {
  identifier = "mydb"
  
  password = jsondecode(data.aws_secretsmanager_secret_version.db_password.secret_string)["password"]
  
  # Other configuration...
}
```

**Use environment variables:**

```bash
export TF_VAR_database_password="supersecret"
terraform apply
```

---

## 25. Debugging Terraform

**Enable detailed logging:**

```bash
export TF_LOG=DEBUG
terraform apply
```

Log levels: TRACE, DEBUG, INFO, WARN, ERROR

**Log to file:**

```bash
export TF_LOG=DEBUG
export TF_LOG_PATH=terraform.log
terraform apply
```

**Disable logging:**

```bash
unset TF_LOG
unset TF_LOG_PATH
```

**Refresh state:**

```bash
terraform refresh
```

**Validate configuration:**

```bash
terraform validate
```

**Graph dependencies:**

```bash
terraform graph | dot -Tpng > graph.png
```

Requires Graphviz to be installed.

---

## 26. Common Terraform Patterns

### Dynamic Blocks

Create repeated nested blocks programmatically.

```hcl
variable "ingress_rules" {
  type = list(object({
    from_port   = number
    to_port     = number
    protocol    = string
    cidr_blocks = list(string)
  }))
  
  default = [
    {
      from_port   = 80
      to_port     = 80
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    },
    {
      from_port   = 443
      to_port     = 443
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  ]
}

resource "aws_security_group" "web" {
  name   = "web-sg"
  vpc_id = aws_vpc.main.id
  
  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
}
```

### Lifecycle Rules

Control resource behavior.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  lifecycle {
    create_before_destroy = true
    prevent_destroy       = false
    ignore_changes        = [tags]
  }
}
```

**create_before_destroy**: Creates replacement before destroying original

**prevent_destroy**: Prevents accidental deletion

**ignore_changes**: Ignores changes to specified attributes

### Provisioners

Run scripts on resources. Use sparingly as they are not declarative.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  provisioner "local-exec" {
    command = "echo ${self.public_ip} >> ips.txt"
  }
  
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx",
    ]
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
}
```

### Null Resources

Execute provisioners without creating resources.

```hcl
resource "null_resource" "setup" {
  triggers = {
    always_run = timestamp()
  }
  
  provisioner "local-exec" {
    command = "echo 'Setup complete'"
  }
}
```

---

## 27. Terraform Testing

### Terraform Validate

```bash
terraform validate
```

### Terraform Plan

```bash
terraform plan
```

### Terraform Console

Interactive console for testing expressions.

```bash
terraform console
```

```hcl
> var.region
"eu-west-1"

> aws_instance.web.public_ip
"54.123.45.67"

> length(var.availability_zones)
2
```

### Terratest

Go-based testing framework for Terraform.

**Example test:**

```go
package test

import (
    "testing"
    "github.com/gruntwork-io/terratest/modules/terraform"
    "github.com/stretchr/testify/assert"
)

func TestTerraformBasic(t *testing.T) {
    terraformOptions := &terraform.Options{
        TerraformDir: "../",
        Vars: map[string]interface{}{
            "instance_type": "t2.micro",
        },
    }
    
    defer terraform.Destroy(t, terraformOptions)
    terraform.InitAndApply(t, terraformOptions)
    
    instanceID := terraform.Output(t, terraformOptions, "instance_id")
    assert.NotEmpty(t, instanceID)
}
```

---

## 28. Upgrading Terraform Versions

**Check current version:**

```bash
terraform version
```

**Update provider versions:**

```bash
terraform init -upgrade
```

**Lock file:**

Terraform creates `.terraform.lock.hcl` to lock provider versions.

**Update specific provider:**

```bash
terraform init -upgrade=true
```

**Migration considerations:**

- Read release notes for breaking changes
- Test in non-production environments first
- Update provider constraints in configuration
- Review deprecated features
- Run `terraform plan` before applying

---

## 29. Best Practices

### Code Organization

- Use modules for reusable components
- Separate environments into different directories or workspaces
- Keep configurations small and focused
- Use consistent naming conventions
- Store related resources in the same file

### State Management

- Always use remote state for teams
- Enable state locking with DynamoDB (AWS) or similar
- Never commit state files to version control
- Back up state files regularly
- Use separate state files for different environments

### Security

- Never commit secrets to version control
- Use variable files (.tfvars) that are gitignored
- Mark sensitive outputs and variables
- Use IAM roles instead of access keys where possible
- Implement least privilege access
- Enable MFA for state bucket access
- Encrypt state files at rest

### Version Control

- Use meaningful commit messages
- Review plans before applying
- Tag releases
- Use pull requests for changes
- Document major changes

### Configuration

- Use variables for values that change
- Provide sensible defaults
- Add descriptions to variables and outputs
- Use data sources instead of hardcoding values
- Implement validation rules for variables

### Performance

- Use `-target` for specific resources during development
- Minimize provider calls
- Use `-parallelism` to control concurrent operations
- Cache plugins locally

### Documentation

- Add comments for complex logic
- Document module inputs and outputs
- Maintain a README for each module
- Include examples in documentation
- Keep architecture diagrams updated

---

## 30. Common Issues and Solutions

### State Lock Error

```
Error: Error acquiring the state lock
```

**Solution:**

```bash
terraform force-unlock <lock-id>
```

Only use this if you are certain no other process is running.

### Provider Plugin Error

```
Error: Could not load plugin
```

**Solution:**

```bash
rm -rf .terraform
terraform init
```

### State Drift

Resources changed outside Terraform.

**Detect drift:**

```bash
terraform plan -refresh-only
```

**Update state:**

```bash
terraform apply -refresh-only
```

### Circular Dependency

**Solution:**

Break the dependency using data sources or refactor resources.

### Resource Already Exists

**Solution:**

Import the existing resource:

```bash
terraform import aws_instance.web i-1234567890abcdef0
```

Or remove from state and let Terraform manage it:

```bash
terraform state rm aws_instance.web
```

### Too Many API Calls

**Solution:**

Reduce parallelism:

```bash
terraform apply -parallelism=5
```

---

## 31. Advanced Topics

Once you master the basics, explore these topics:

- **Terraform Cloud**: Collaboration, remote execution and policy enforcement
- **Sentinel**: Policy as code for Terraform Cloud
- **CDK for Terraform**: Use programming languages instead of HCL
- **Terragrunt**: Wrapper for managing multiple Terraform modules
- **Custom Providers**: Build your own Terraform providers
- **Module Registry**: Publish and share modules
- **Import Existing Infrastructure**: Migrate infrastructure to Terraform
- **State Migration**: Move state between backends
- **Multi-Region Deployments**: Manage resources across regions
- **Disaster Recovery**: State backup and recovery strategies

---

## 32. Useful Resources

**Official Documentation:**

- [terraform.io/docs](https://www.terraform.io/docs)
- [registry.terraform.io](https://registry.terraform.io)

**Provider Documentation:**

- [AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Azure Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Google Cloud Provider](https://registry.terraform.io/providers/hashicorp/google/latest/docs)

**Learning Resources:**

- HashiCorp Learn tutorials
- Terraform examples on GitHub
- AWS Well-Architected Framework
- Cloud provider best practices

---

## Key Takeaway

Terraform transforms infrastructure management by treating infrastructure as code. Understanding providers, resources, state and modules gives you the foundation to provision and manage infrastructure consistently across any cloud platform. Start with simple configurations, use modules to organize code and implement proper state management as your infrastructure grows. Terraform enables teams to collaborate effectively, version infrastructure changes and maintain reproducible environments across development, staging and production.

---