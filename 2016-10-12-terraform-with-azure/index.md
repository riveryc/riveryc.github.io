# Terraform with Azure


Start playing Terraform …

Get it downloaded & saved it to a local folder: C:\bin

Put this path into local $PATH var by running:

<!--more-->

```dos
$env:Path += ";C:\bin"
```

Restart a command line, type in `terraform`:

```dos
C:\Users\River>terraform
usage: terraform [--version] [--help] <command> [args]
The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.
Common commands:
    apply              Builds or changes infrastructure
    destroy            Destroy Terraform-managed infrastructure
    fmt                Rewrites config files to canonical format
    get                Download and install modules for the configuration
    graph              Create a visual graph of Terraform resources
    import             Import existing infrastructure into Terraform
    init               Initializes Terraform configuration from a module
    output             Read an output from a state file
    plan               Generate and show an execution plan
    push               Upload this Terraform module to Atlas to run
    refresh            Update local state file against real resources
    remote             Configure remote state storage
    show               Inspect Terraform state or plan
    taint              Manually mark a resource for recreation
    untaint            Manually unmark a resource as tainted
    validate           Validates the Terraform files
    version            Prints the Terraform version
All other commands:
    state              Advanced state management
Once you can see above, "Terraform" is correctly installed.
```

Due to I only have Azure account, so I'll start playing with this...

* Create a terraform file `terraform.tf` with following content

```hcl
# Configure the Microsoft Azure Provider
provider “azurerm” {
 subscription_id = “subscription_id” ##Azure Account details, click the ‘subscription’, copy the guid id
 client_id = “client_id” ##Refers to step 3.1
 client_secret = “client_secret” ##Refers to step 3.2
 tenant_id = “tenant_id” ##Refers to step 3.3
}
```

Getting above values from Azure need manual work, but once only.

* Login to Azure legacy portal: <https://manage.windowsazure.com>
* Getting ‘client_id’, ‘client_secret’ and ‘tenant_id’:
  * client_id
    * Select “Active Directory”
    * Select “Applications”
    * Select “Add” — “Add an application my organization is developing”
    * ![screenshot](/img/post/20161012/terraform-with-azure-1.png)
    * Give it a good name “Terrabot”
    * ![screenshot](/img/post/20161012/terraform-with-azure-2.png)
    * Input any 2 valid URLs, we are not going to consume them at all, so no need to be serious:
    * ![screenshot](/img/post/20161012/terraform-with-azure-3.png)
    * You can get the client_id from here:
    * ![screenshot](/img/post/20161012/terraform-with-azure-4.png)
  * client_secret
    * Scroll down to ‘keys’ secition, select duration
    * ![screenshot](/img/post/20161012/terraform-with-azure-5.png)
    * Click “Save” at the bottom of the page, and you can see the key from the same location:
    * ![screenshot](/img/post/20161012/terraform-with-azure-6.png)
  * tenant_id
    * Click “View Endpoints” at the bottom of the page:
    * ![screenshot](/img/post/20161012/terraform-with-azure-7.png)
    * Find “OAUTH 2.0 AUTHORIZATION ENDPOINT”, and copy the key from the url, you can find it from here: <https://login.microsoftonline.com/tenantid/oauth2/authorize>

Start from a basic resource:

```hcl
# Create a resource group
resource "azurerm_resource_group" "au_prod" {
    name     = "au_prod"
    location = "Australia East"
}
```

Save the file, and run "terraform plan":

```output
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but
will not be persisted to local or remote state storage.
The Terraform execution plan has been generated and is shown below.
Resources are shown in alphabetical order for quick scanning. Green resources
will be created (or destroyed and then created if an existing resource
exists), yellow resources are being changed in-place, and red resources
will be destroyed. Cyan entries are data sources to be read.
Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
+ azurerm_resource_group.au_prod
    location: "australiaeast"
    name:     "au_prod"
Plan: 1 to add, 0 to change, 0 to destroy.
```

Plan shows what will `terraform` do if run with `apply` command.

We can do `terraform apply` to get the resource implemented.

Here is the simple resource description for 10 servers & multiple networks:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "subscription_id"
  client_id       = "client_id"
  client_secret   = "client_secret"
  tenant_id       = "tenant_id"
}
# Create a resource group
resource "azurerm_resource_group" "au_prod" {
    name     = "au_prod"
    location = "Australia East"
}
resource "azurerm_virtual_network" "au_network" {
    name                = "au_network"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    address_space       = ["192.168.0.0/16"]
    location            = "${azurerm_resource_group.au_prod.location}"
    dns_servers         = ["8.8.8.8", "8.8.4.4"]
}
resource "azurerm_subnet" "au_subnet0" {
    name                = "au_subnet0"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    virtual_network_name = "${azurerm_virtual_network.au_network.name}"
    address_prefix      = "192.168.0.0/24"
}
resource "azurerm_subnet" "au_subnet1" {
    name                = "au_subnet1"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    virtual_network_name = "${azurerm_virtual_network.au_network.name}"
    address_prefix      = "192.168.1.0/24"
}
resource "azurerm_subnet" "au_subnet2" {
    name                = "au_subnet2"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    virtual_network_name = "${azurerm_virtual_network.au_network.name}"
    address_prefix      = "192.168.2.0/24"
}
#Network Security group
resource "azurerm_network_security_group" "au_fw" {
    name = "au_nsg_win"
    location            = "${azurerm_resource_group.au_prod.location}"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
security_rule {
        name = "allow-rdp"
        priority = 1000
        direction = "Inbound"
        access = "Allow"
        protocol = "Tcp"
        source_port_range = "*"
        destination_port_range = "3389"
        source_address_prefix = "*"
        destination_address_prefix = "*"
    }
}
#Storage Account
resource "azurerm_storage_account" "au_storage_acc" {
    name                = "ultstorageriver"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    location            = "${azurerm_resource_group.au_prod.location}"
    account_type        = "Standard_LRS"
}
#Storage Container
resource "azurerm_storage_container" "au_storage_container" {
    name = "vhds"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    storage_account_name = "${azurerm_storage_account.au_storage_acc.name}"
    container_access_type = "private"
    depends_on          = ["azurerm_storage_account.au_storage_acc"]
}
#Repeat part
variable "count" {
  default = 10
}
#public IP
resource "azurerm_public_ip" "au_public_ip" {
    count               = "${var.count}"
    name                = "${format("pip%02d", count.index + 1)}"
    location            = "${azurerm_resource_group.au_prod.location}"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    public_ip_address_allocation = "dynamic"
}
#Network Interface
resource "azurerm_network_interface" "au_netinterface" {
    count               = "${var.count}"
    name                = "${format("nic%02d", count.index + 1)}"
    location            = "${azurerm_resource_group.au_prod.location}"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
ip_configuration {
        name            = "ipconfig1"
        subnet_id       = "${azurerm_subnet.au_subnet2.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id = "${element(azurerm_public_ip.au_public_ip.*.id, count.index)}"
    }
}
#Create VM
resource "azurerm_virtual_machine" "au_vms" {
    count               = "${var.count}"
    name                = "${format("vm%02d", count.index + 1)}"
    location            = "${azurerm_resource_group.au_prod.location}"
    resource_group_name = "${azurerm_resource_group.au_prod.name}"
    network_interface_ids = ["${element(azurerm_network_interface.au_netinterface.*.id, count.index)}"]
    vm_size             = "Basic_A0"
storage_image_reference {
        publisher       = "MicrosoftWindowsServer"
        offer           = "WindowsServer"
        sku             = "2012-R2-Datacenter"
        version         = "latest"
    }
storage_os_disk {
        name            = "${format("os_audisk%02d", count.index + 1)}"
        vhd_uri         = "${azurerm_storage_account.au_storage_acc.primary_blob_endpoint}${azurerm_storage_container.au_storage_container.name}/${format("os_audisk%02d.vhd", count.index + 1)}"
        caching         = "ReadWrite"
        create_option   = "FromImage"
    }
os_profile {
        computer_name   = "${format("vm%02d", count.index + 1)}"
        admin_username  = "river"
        admin_password  = "pAssw0rd!@"
    }
    depends_on          = ["azurerm_network_interface.au_netinterface", "azurerm_network_security_group.au_fw", "azurerm_storage_container.au_storage_container"]
}
```

Have fun~

