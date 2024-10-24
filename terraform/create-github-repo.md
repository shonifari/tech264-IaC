
# Creating a GitHub Repository Using Terraform

## Step-by-Step Guide

### 1. Set Up Your GitHub Personal Access Token

- Generate a Personal Access Token (PAT) from GitHub with the necessary permissions (e.g., `repo` scope).
    ![alt text](<./images/Screenshot 2024-10-23 153602.png>)
- Store the token securely, as you'll need it to authenticate Terraform with GitHub.

### 2. Create a Terraform Configuration File

- Create a new directory for your Terraform configuration and navigate into it.
- Create a file named `main.tf` and add the following content:

```hcl
provider "github" {
  token = var.github_token
}

resource "github_repository" "example" {
  name        = "example-repo"
  description = "This is an example repository created with Terraform"
  visibility  = "public"
  auto_init   = true
}
```

### 3. Define Variables

- Create a file named `variables.tf` to define the `github_token` variable:

```hcl
variable "github_token" {
  description = "The GitHub token for API access"
  type        = string
  sensitive   = true
}
```

### 4. Create a Terraform Variables File

- Create a file named `terraform.tfvars` to provide the value for the `github_token` variable:

```hcl
github_token = "your_personal_access_token"
```

### 5. Initialize and Apply Terraform Configuration

- Run the following commands in your terminal:

```sh
terraform init
terraform apply
```

- Review the plan and confirm the apply step by typing `yes`.

## Summary

This configuration will create a new public GitHub repository named `example-repo` with an initial commit. The `github_token` variable is used to authenticate with the GitHub API.

By following these steps, you can automate the creation of GitHub repositories using Terraform.

![alt text](<./images/Screenshot 2024-10-23 155726.png>)

---
