# Terraform Overview

## What is Terraform? What is it used for?

Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp. It allows users to define and provision data center infrastructure using a high-level configuration language called HashiCorp Configuration Language (HCL) or JSON. Terraform is used to build, change, and version infrastructure safely and efficiently across various cloud providers and on-premises environments¹.

## Why use Terraform? The benefits?

Using Terraform offers several benefits:

1. **Multi-Cloud Support**: Terraform can manage infrastructure across multiple cloud providers, including AWS, Azure, and Google Cloud Platform.
2. **Infrastructure as Code**: It allows infrastructure to be described in code, making it easy to version, reuse, and share.
3. **Automation**: Automates the provisioning and management of infrastructure, reducing manual errors and increasing efficiency.
4. **Consistency**: Ensures consistent environments by using the same configuration to provision infrastructure.
5. **Scalability**: Easily scales infrastructure up or down based on demand².

## Alternatives to Terraform

Several alternatives to Terraform include:

1. **Pulumi**: Uses general-purpose programming languages to define infrastructure.
2. **AWS CloudFormation**: A service that provides a common language for describing and provisioning AWS infrastructure resources.
3. **Ansible**: An open-source automation tool for configuration management, application deployment, and task automation.
4. **Google Deployment Manager**: Allows users to specify all the resources needed for their application in a declarative format.
5. **Azure Resource Manager (ARM) Templates**: Provides a way to deploy and manage Azure resources using JSON templates[^10^].

## Who is using Terraform in the industry?

Terraform is widely used across various industries. Some notable companies using Terraform include:

- **3M**
- **Allstate**
- **Samsung**
- **H&R Block**
- **GitHub**
- **Druva**
- **Pure Storage Inc**¹⁹[^20^].

## In IaC, what is orchestration? How does Terraform act as "orchestrator"?

Orchestration in Infrastructure as Code (IaC) refers to the automated arrangement, coordination, and management of complex computer systems, middleware, and services. Terraform acts as an orchestrator by managing the lifecycle of infrastructure resources, ensuring that they are created, updated, and destroyed in the correct order based on defined dependencies¹⁶.

## Best practice supplying AWS credentials to Terraform

### Order of AWS Credentials Lookup

Terraform looks up AWS credentials in the following order:

1. **Environment Variables**: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and optionally `AWS_SESSION_TOKEN`.
2. **Shared Credentials File**: Typically located at `~/.aws/credentials`.
3. **AWS Config File**: Typically located at `~/.aws/config`.
4. **EC2 Instance Metadata**: Automatically provided by AWS when running on an EC2 instance¹⁴.

### Best Practices

- **Use Environment Variables**: Set `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` as environment variables.
- **Use IAM Roles**: When running Terraform on AWS services like EC2, use IAM roles to provide temporary credentials.
- **Avoid Hardcoding Credentials**: Never hardcode AWS credentials directly in Terraform configuration files or version control.

## Why use Terraform for different environments (e.g., production, testing, etc)?

Using Terraform for different environments ensures consistency and repeatability across development, testing, and production environments. It allows teams to:

- **Version Control**: Track changes and maintain history of infrastructure configurations.
- **Automate Deployments**: Reduce manual intervention and errors by automating infrastructure provisioning.
- **Ensure Consistency**: Use the same configuration files to provision identical environments, reducing discrepancies and bugs².

---
