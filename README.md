# Terraform

Infrastructure as code is the idea of defining everything you need to build in a programming language like Terraform (or Ansible etc.) and then use that code to stand up your infrastructure.

### Why infrastructure as code?
-  Helps you maintain an audit trail of every single change that goes into your environment.
-  Steps are defined as code, eliminating human intervention and in turn human errors.
-  You can test your code by building automated pipelines that will stand up your infrastructure and run tests against it.
-  Can sync up Dev, Staging and production. Ideally all the enviroments should mirror each other to ensure the programs/scripts run seamlessly.
-  You can easily re-build your infrastructure in case of failure. Imagine spinning up 12 different VMs, 2 storages etc. manually.


### Terraform Basics

Terraform maintains the set of instructions in Terraform configuration files. They end with `.tf` extensions.

Let's consider the following file for our example - 

```python
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "3.5.0"
    }
  }
}

provider "google" {
  credentials = file("<NAME>.json")

  project = "<PROJECT_ID>"
  region  = "us-central1"
  zone    = "us-central1-c"
}

resource "google_compute_network" "vpc_network" {
  name = "terraform-network"
}

```

Terraform divides information into blocks, defined in curly braces `{}`. There are three main blocks - `terraform, provider, resource`.

#### Terraform Block

The `terraform {}` block contains Terraform settings, including the required providers Terraform will use to provision your infrastructure.

```python
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "3.5.0"
    }
  }
}
```

For each provider, the `source` attribute defines an optional hostname, a namespace, and the provider type. Terraform installs providers from the Terraform Registry by default. In this example configuration, the google provider's source is defined as hashicorp/google, which is shorthand for registry.terraform.io/hashicorp/google.

You can also define a version constraint for each provider in the required_providers block. The version attribute is optional.

#### Providers

The `provider` block configures the specified provider, in this case `google`. A provider is a plugin that Terraform uses to create and manage your resources. You can define multiple provider blocks in a Terraform configuration to manage resources from different providers.

```python
provider "google" {
  credentials = file("<NAME>.json")

  project = "<PROJECT_ID>"
  region  = "us-central1"
  zone    = "us-central1-c"
}
```


#### Resource

The `resource` block is used to define components of your infrastructure. A resource might be -  
- A physical component like a server
- A logical component like a Heroku Application

Resource blocks have two strings before the block -  
-   Resource type
-   Resource name

```python
resource "google_compute_network" "vpc_network" {
  name = "terraform-network"
}
```
In this example, the resource type is `google_compute_network` and the resource name is `vpc_network`. Together the resource type and the resource name provide and unique ID for the resource `google_compute_network.vpc_network`. The ID must be unique within the module.

The prefix of the type maps to the name of the provider. In this example, terraform manages the `google_compute_network` with the `google` provider.
`compute` indicates the GCP product family and `instance` is the resource name. 

The resource name is used to refer to the resource from elsewhere in the same Terraform module, but has no significance outside that module's scope.

#### Inspect State
Terraform stores the IDs and properties of the resources it manages in the `terraform.tfstate` file, so that it can update or destroy those resources going forward.  
The Terraform state file is the only way Terraform can track which resources it manages, and often contains sensitive information.

#### Input Variables

To make your configuration more dynamic and flexible you can include variables. If a `default` value is set, then the variable becomes optional. Else it is a required variable. 

```python
variable "project" { }

variable "region" {
  default = "us-central1"
}

variable "zone" {
  default = "us-central1-c"
}

```

You can populate variables using command line or via a file. Terraform automatically loads files called `terraform.tfvars` or matching `*.auto.tfvars` in the working directory when running operations.

### Terraform Commands

Intialize the directory and download the necessary plugins and provider details:  
```bash
terraform init
```

Format your configuration files:  
```bash
terraform fmt
``` 

Validate your configuration files for syntax and internal consistency:  
```bash
terraform validate
```

Create and display apply plan:  
```bash
terraform plan
```

Apply the changes to your infrastructure:
```bash
terraform apply
```

Inspect the current state:  
```bash
terraform show
```

Terminate resources:  
```bash
terraform destroy
```