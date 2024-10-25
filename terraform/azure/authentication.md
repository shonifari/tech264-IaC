# Terraform Azure authentication

- [Terraform Azure authentication](#terraform-azure-authentication)
  - [Azure CLI](#azure-cli)
    - [Prerequisites](#prerequisites)
    - [Steps to Authenticate](#steps-to-authenticate)

## Azure CLI

### Prerequisites

- Ensure you have the Azure CLI installed. You can download and install it from here.

### Steps to Authenticate

1. **Open your terminal**:
   - On Windows, you can use Command Prompt, PowerShell, or Windows Terminal.
   - On macOS or Linux, use your preferred terminal application.

2. **Login to Azure**:
   - Run the following command to login interactively:

     ```sh
     az login
     ```

   - This will open a web browser window where you can enter your Azure credentials.

3. **Print your account data** :
   - After logging in, you can list your account data:

     ```sh
     az account show
     ```

   - Output:

     ```text
     {
        "environmentName": "AzureCloud",
        "homeTenantId": "*********",
        "id": "*********",
        "isDefault": true,
        "managedByTenants": [],
        "name": "*********",
        "state": "Enabled",
        "tenantDefaultDomain": "*********",
        "tenantDisplayName": "*********",
        "tenantId": "*********",
        "user": {
            "name": "*********",
            "type": "user"
        }
     ```

4. **Edit your terraform code**

     ```sh
    provider "azurerm" {
        features {}
        
        # Authenticate through Azure CLI
        use_cli                         = true
        subscription_id                 = var.subscription_id
        resource_provider_registrations = "none"
    }

     ```
