# Creating a resource

- [Creating a resource](#creating-a-resource)
  - [EC2 Instance](#ec2-instance)
    - [Provider](#provider)
    - [Resource](#resource)
    - [Summary](#summary)

> **IMPORTANT**: Follow this [terraform gitignore guide](terraform-gitignore.md) to prevent accidentally sharing sensitive informations.

## EC2 Instance

### Provider

```hcl
provider "aws" {
    # Which region we use
    region = "eu-west-1"
}
```

- **provider "aws"**: Specifies the AWS provider.
- **region**: Sets the AWS region to `eu-west-1` (Ireland).

### Resource

```hcl
resource "aws_instance" "app_instance" {
    # AMI ID ami-0c1c30571d2dae5c9 (for Ubuntu 22.04 LTS)
    ami = "ami-0c1c30571d2dae5c9"

    # Instance type
    instance_type = "t2.micro"

    # Public IP
    associate_public_ip_address = true

    # Name the resource
    tags = {
        Name = "tech264-karis-tf-app-vm"
    }

    user_data = <<-EOF
              #!/bin/bash
              echo "Hello, World!"
              EOF
}
```

- **resource "aws_instance" "app_instance"**: Defines an AWS EC2 instance resource named `app_instance`.
- **ami**: Specifies the Amazon Machine Image (AMI) ID to use, in this case, `ami-0c1c30571d2dae5c9`, which corresponds to Ubuntu 22.04 LTS.
- **instance_type**: Sets the instance type to `t2.micro`..
- **associate_public_ip_address**: Ensures the instance is assigned a public IP address.
- **tags**: Adds metadata to the instance, with a tag named `Name` set to `tech264-karis-tf-app-instance`.
- **user_data**: Contains a script that runs when the instance starts. This script writes "Hello, World!" to a file.

### Summary

This Terraform configuration creates an EC2 instance in the `eu-west-1` region using a specified AMI for Ubuntu 22.04 LTS. The instance is of type `t2.micro`, is assigned a public IP address, and is tagged with a name. Additionally, a simple user data script is provided to run on instance startup.

---
