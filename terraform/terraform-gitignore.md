
# Important files to **NEVER** push

- [Important files to **NEVER** push](#important-files-to-never-push)
  - [.terraform folder](#terraform-folder)
  - [State files](#state-files)
  - [Variables files](#variables-files)
  - [Override files](#override-files)
    - [Summary](#summary)

Your .gitignore file should have the following:

```bash
# .terraform folder
.terraform/

# State files - Most important to avoid
terraform.tfstate
terraform.tfstate.backup

# Variables files
*.tfvars
*.auto.tfvars
variable.tf

# Override files
override.tj
override.tj.json

```

## .terraform folder

- **`.terraform/`**: This line ignores the `.terraform` directory, which contains local Terraform state and cache files. Ignoring this directory helps prevent unnecessary files from being tracked by Git, reducing repository bloat.

## State files

- **`terraform.tfstate`** and **`terraform.tfstate.backup`**: These files store the state of your Terraform-managed infrastructure. They contain sensitive information and are frequently updated, so it's crucial to exclude them from version control to avoid conflicts and potential security risks.

## Variables files

- **`*.tfvars`**, **`*.auto.tfvars`**, and **`variable.tf`**: These files typically contain variable definitions, including sensitive data like passwords and API keys. Excluding them from version control helps protect sensitive information and ensures that environment-specific configurations are not shared.

## Override files

- **`override.tj`** and **`override.tj.json`**: These files are used to override default configurations locally. They are usually specific to individual environments or developers and should not be tracked in version control to avoid conflicts.

### Summary

This `.gitignore` file is designed to exclude files and directories that are either sensitive, environment-specific, or unnecessary for version control. By doing so, it helps maintain a clean and secure repository.
