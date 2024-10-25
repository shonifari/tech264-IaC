# 2 tier app deployment

- [2 tier app deployment](#2-tier-app-deployment)
  - [Authentication](#authentication)
  - [Networking](#networking)
    - [Virtual Network](#virtual-network)
    - [Public Subnet](#public-subnet)
    - [Private Subnet](#private-subnet)
  - [Database](#database)
    - [Database Network Security Group (NSG)](#database-network-security-group-nsg)
    - [Database Network Interface Controller (NIC)](#database-network-interface-controller-nic)
    - [Database NIC - NSG association](#database-nic---nsg-association)
    - [Database Virtual Machine](#database-virtual-machine)
  - [Application](#application)
    - [Application Network Security Group (NSG)](#application-network-security-group-nsg)
    - [Application Public IP](#application-public-ip)
    - [Application Network Interface Controller (NIC)](#application-network-interface-controller-nic)
    - [Application NIC - NSG association](#application-nic---nsg-association)
    - [Application Virtual Machine](#application-virtual-machine)

## Authentication

There are different ways to authenticate and most can be found in the [Azure authentication methods guide](authentication.md). What we are using is the [Azure CLI](authentication.md#azure-cli).

Change your `provider` configuration as followint

```sh
provider "azurerm" {
    features {}
    
    # Authenticate through Azure CLI
    use_cli                         = true
    subscription_id                 = var.subscription_id
    resource_provider_registrations = "none"
}

```

- `use_cli`: Tells terraform we are authenticating through **CLI**
- `subscription_id`: Provide the correct **subscription ID**
- `resource_provider_registrations`: Setting this to ***none*** stops terraform from trying to create the resource

## Networking

### Virtual Network

To declare a Virtual Network resource we use the following code:

```sh
resource "azurerm_virtual_network" "my_vnet" {

  name                = "tech264-karis-tf-2-subnet-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.resource_group_location
  resource_group_name = var.resource_group_name

  tags = {
    Owner = "Karis"
  }

}
```

### Public Subnet

When creating a subnet resource thats not part of a VNet block, it's important to link the two resources togeather thrugh the `virtual_network_name` variable.
To create a subnet we use the following code:

```sh
# Public subnet
resource "azurerm_subnet" "public_subnet" {
  name                 = "public-subnet"
  resource_group_name  = var.resource_group_name
  address_prefixes     = ["10.0.2.0/24"]

  # Link subnet to VNet
  virtual_network_name = azurerm_virtual_network.my_vnet.name

}
```

### Private Subnet

A private subnet follows the same process as a [public subnet](#public-subnet). In order to make the subnet private we need to enable private network policies through the  `private_endpoint_network_policies` variable

```sh
# Private subnet

resource "azurerm_subnet" "private_subnet" {
  name                              = "private-subnet"
  resource_group_name               = var.resource_group_name
  address_prefixes                  = ["10.0.3.0/24"]
  
  # Link subnet to VNet
  virtual_network_name              = azurerm_virtual_network.my_vnet.name
  # Enable private network
  private_endpoint_network_policies = "Enabled"
}
```

## Database

### Database Network Security Group (NSG)

```sh
# DB Network Segurity Group

resource "azurerm_network_security_group" "db_vm_nsg" {

  name                = "tech264-karis-tf-db-vm-nsg"
  location            = var.resource_group_location
  resource_group_name = var.resource_group_name
  tags = {
    Owner = "Karis"
  }

 #  **ADD SECURITY RULES HERE**

}
```

- #### Allow SSH rule

    ```sh
    # Allow SSH rule
    security_rule {
        name                       = "Allow_SSH"
        priority                   = 300
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"

    }
    ```

- #### Allow MongroDB rule

    ```sh
    # Allow MongoDB rule
    security_rule {
        name                       = "Allow_MondoDB"
        priority                   = 320
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "27017"
        source_address_prefix      = azurerm_subnet.public_subnet.address_prefixes[0]
        destination_address_prefix = "*"

    }
    ```

- #### Deny evrything rule

    ```sh
    # Deny everything rule
    security_rule {
        name                       = "Deny_everything"
        priority                   = 500
        direction                  = "Inbound"
        access                     = "Deny"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "*"
        destination_address_prefix = "*"

    }

    ```

### Database Network Interface Controller (NIC)

In order to be able to comunicate with our DB Virtual Machine we need to configure its **IP address** in our **Network Interface Controller**

```sh
resource "azurerm_network_interface" "db_nic" {
  name                = "tech264-karis-tf-db-nic"
  location            = var.resource_group_location
  resource_group_name = var.resource_group_name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.private_subnet.id
    private_ip_address_allocation = "Dynamic"
  }

  tags = {
    Owner = "Karis"
  }
}
```

- `subnet_id`: Here we link this **NIC** to the [private subnet](#private-subnet).

### Database NIC - NSG association

In Terraform we need to explicitly declare an association between [DB NIC](#database-network-interface-controller-nic) and [DB NSG](#database-network-security-group-nsg) using the parameters `network_interface_id` and `network_security_group_id` as follow:

```sh
# NIC - NSG association
resource "azurerm_network_interface_security_group_association" "db_nic_2_nsg" {
  network_interface_id      = azurerm_network_interface.db_nic.id
  network_security_group_id = azurerm_network_security_group.db_vm_nsg.id
}
```

### Database Virtual Machine

This Terraform configuration defines an Azure Linux virtual machine resource named `db_vm`. Below is a breakdown of each part of the configuration:

```sh
# DB Virtual Machine
resource "azurerm_linux_virtual_machine" "db_vm" {
  name                = "tech264-karis-tf-db-vm"
  resource_group_name = var.resource_group_name
  location            = var.resource_group_location
  size                = var.vm_instance_size
  admin_username      = var.vm_username


  network_interface_ids = [
    azurerm_network_interface.db_nic.id,
  ]

  admin_ssh_key {
    username   = var.vm_username
    public_key = var.vm_ssh_public_key
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = var.vm_disk_type
  }

  source_image_id = var.vm_db_image_id

  tags = {
    Owner = "Karis"
  }
}
```

- **Resource Type**: `azurerm_linux_virtual_machine`
- **Resource Name**: `db_vm`
- **Name**: The name of the virtual machine is set to `tech264-karis-tf-db-vm`.
- **Resource Group**: The virtual machine is placed in the resource group specified by the variable `var.resource_group_name`.
- **Location**: The location of the virtual machine is specified by the variable `var.resource_group_location`.
- **Size**: The size of the virtual machine instance is defined by the variable `var.vm_instance_size`.
- **Admin Username**: The admin username for the virtual machine is set using the variable `var.vm_username`.
- **Network Interface IDs**: The virtual machine is associated with the network interface identified by `azurerm_network_interface.db_nic.id`.
- **Admin SSH Key**: The SSH key for the admin user is specified using the variables `var.vm_username` and `var.vm_ssh_public_key`.
- **OS Disk Configuration**:
  - **Caching**: Set to `ReadWrite`.
  - **Storage Account Type**: Defined by the variable `var.vm_disk_type`.
- **Source Image ID**: The ID of the source image for the virtual machine is specified by the variable `var.vm_db_image_id`.
- **Tags**: The virtual machine is tagged with an owner tag set to `Karis`.

## Application

### Application Network Security Group (NSG)

```sh
## NSG

# App Network Segurity Group
# - allow: SSH (22), HTTP (80), NodeJS port (3000) from any
resource "azurerm_network_security_group" "app_vm_nsg" {

  name                = "tech264-karis-tf-app-vm-nsg"
  location            = var.resource_group_location
  resource_group_name = var.resource_group_name
  tags = {
    Owner = "Karis"
  }

  #  **ADD SECURITY RULES HERE**
}
```

- #### Allow SSH rule

    ```sh
    # Allow SSH rule
    security_rule {
        name                       = "Allow_SSH"
        priority                   = 300
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"


    }
    ```

- #### Allow HTTP rule

    ```sh


    # Allow HTTP rule
    security_rule {
        name                       = "Allow_HTTP"
        priority                   = 320
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "80"
        source_address_prefix      = "*"
        destination_address_prefix = "*"

    }
    ```

- #### Allow Port 3000 rule

    ```sh

    # Allow Port 3000 rule
    security_rule {
        name                       = "Allow_port_3000"
        priority                   = 330
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "3000"
        source_address_prefix      = "*"
        destination_address_prefix = "*"

    }
    ```

### Application Public IP

To assing our application a **public ip** we need to create it as a stand-alone resource

```sh
# Public IP
resource "azurerm_public_ip" "app_public_ip" {
  name                = "tech264-karis-app-public-ip"
  location            = var.resource_group_location
  resource_group_name = var.resource_group_name
  allocation_method   = "Static"
}
```

- `allocatin_method`: Define if the **IP** should change at every VM startup

### Application Network Interface Controller (NIC)

In order to be able to comunicate with our App Virtual Machine we need to configure its **IP address** in our **Network Interface Controller**. In addition to our [DB NIC](#database-network-interface-controller-nic) we need to allocate our application public ip using the `public_ip_address_id` parameter and providing the ID of the  [Public IP resource](#application-public-ip) we created earlier.

```sh
# NIC
resource "azurerm_network_interface" "app_nic" {
  name                = "tech264-karis-tf-app-nic"
  location            = var.resource_group_location
  resource_group_name = var.resource_group_name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.public_subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.app_public_ip.id
  }

  tags = {
    Owner = "Karis"
  }
}
```

### Application NIC - NSG association

In Terraform we need to explicitly declare an association between [APP NIC](#application-network-interface-controller-nic) and [APP NSG](#application-network-security-group-nsg) using the parameters `network_interface_id` and `network_security_group_id` as follow:

```sh
# NIC - NSG association

resource "azurerm_network_interface_security_group_association" "app_nic_2_nsg" {
  network_interface_id      = azurerm_network_interface.app_nic.id
  network_security_group_id = azurerm_network_security_group.app_vm_nsg.id
}
```

### Application Virtual Machine

This Terraform configuration defines an Azure Linux virtual machine resource named `app_vm`. Below is a breakdown of each part of the configuration:

```sh
# APP Virtual Machine

resource "azurerm_linux_virtual_machine" "app_vm" {
  name                = "tech264-karis-tf-app-vm"
  resource_group_name = var.resource_group_name
  location            = var.resource_group_location
  size                = var.vm_instance_size
  admin_username      = var.vm_username

  # Dependency from db
  depends_on = [azurerm_linux_virtual_machine.db_vm]

  network_interface_ids = [
    azurerm_network_interface.app_nic.id,
  ]

  admin_ssh_key {
    username   = var.vm_username
    public_key = var.vm_ssh_public_key
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = var.vm_disk_type
  }

  source_image_id = var.vm_app_image_id

  tags = {
    Owner = "Karis"
  }

  user_data = base64encode(templatefile("./scripts/app-vm-image-provision.tftpl",
    { DB_PRIVATE_IP = azurerm_network_interface.db_nic.private_ip_address }
  ))

}
```

- **Resource Type**: `azurerm_linux_virtual_machine`
- **Resource Name**: `app_vm`
- **Name**: The name of the virtual machine is set to `tech264-karis-tf-app-vm`.
- **Resource Group**: The virtual machine is placed in the resource group specified by the variable `var.resource_group_name`.
- **Location**: The location of the virtual machine is specified by the variable `var.resource_group_location`.
- **Size**: The size of the virtual machine instance is defined by the variable `var.vm_instance_size`.
- **Admin Username**: The admin username for the virtual machine is set using the variable `var.vm_username`.
- **Depends On**: This virtual machine depends on the `db_vm` virtual machine, ensuring that `db_vm` is created before `app_vm`.
- **Network Interface IDs**: The virtual machine is associated with the network interface identified by `azurerm_network_interface.app_nic.id`.
- **Admin SSH Key**: The SSH key for the admin user is specified using the variables `var.vm_username` and `var.vm_ssh_public_key`.
- **OS Disk Configuration**:
  - **Caching**: Set to `ReadWrite`.
  - **Storage Account Type**: Defined by the variable `var.vm_disk_type`.
- **Source Image ID**: The ID of the source image for the virtual machine is specified by the variable `var.vm_app_image_id`.
- **Tags**: The virtual machine is tagged with an owner tag set to `Karis`.
- **User Data**: The `user_data` attribute is used to pass a script to the virtual machine. The script is base64 encoded and uses the `templatefile` function to inject the private IP address of the `db_vm` network interface (`azurerm_network_interface.db_nic.private_ip_address`). User data script:

    ```sh
    #!/bin/bash

    REPO="tech264-sparta-app"

    # Set DB_HOST variable 
    export DB_HOST=mongodb://${DB_PRIVATE_IP}:27017/posts
    #export DB_HOST=mongodb://10.0.3.4:27017/posts
    cd /$REPO/app/

    node seeds/seed.js

    pm2 stop all

    pm2 start app.js 
    ```
