# Terraform Basic Commands

- [Terraform Basic Commands](#terraform-basic-commands)
  - [init](#init)
  - [plan](#plan)
  - [apply](#apply)
  - [fmt](#fmt)
  - [destroy](#destroy)


## init

- **Purpose**: Initializes a Terraform working directory.
- **Usage**: Downloads the necessary provider plugins and sets up the backend configuration.
- **Example**:

  ```sh
  terraform init
  ```

## plan

- **Purpose**: Creates an execution plan.
- **Usage**: Shows what actions Terraform will take to achieve the desired state defined in the configuration files.
- **Example**:

  ```sh
  terraform plan
  ```

## apply

- **Purpose**: Applies the changes required to reach the desired state of the configuration.
- **Usage**: Executes the actions proposed in the `terraform plan`.
- **Example**:

  ```sh
  terraform apply
  ```

## fmt

- **Purpose**: Formats Terraform configuration files.
- **Usage**: Ensures the configuration files are properly formatted and consistent.
- **Example**:

  ```sh
  terraform fmt
  ```

## destroy

- **Purpose**: Destroys the Terraform-managed infrastructure.
- **Usage**: Removes all resources defined in the configuration files.
- **Example**:

  ```sh
  terraform destroy
  ```

These commands are fundamental to managing infrastructure with Terraform. Do you need more details on any specific command?