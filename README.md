# terraform
Terraform scripts to create Azure Qatar Landing Zone

## Provision the landing zone (once you have created the Hub and DevTest subscriptions)
* Install terraform 
* Install Azure CLI
* Clone this repository the machine where terraform and Azure CLI is installed. 
* Login to azure from the linux machine CLI (**az login**)
* Get the subscription ID (id value) and tenant ID (tenantId value) for the hub subscription (copy from output of command:   **az list**)
* Create a service principal (**az ad sp create-for-rbac --name=terraform-sp1 --role="Contributor" --scopes="/subscriptions/HUB_SUBSCRIPTION_ID"** )
* copy the values from output of service principal creation. 

### Provision Hub 
* Go to the folder terraform/hub
* Edit the file main.tf 
* In the block : provider "azurerm" change the values of following, and save the file. 
*  subscription_id => Azure subscription ID (from az list command)
*  -> client_id => appId value from az ad sp create-for-rbac command)
*  -> tenand_id => tenant value from az ad sp create-for-rbac command)
* Change the values in variables.tf
* Change the value of customer-name variable to a short customer identifier value (default = "changeme" to default = "xyz")
* Change the other values for variables as necessary. Save the file. 
From the hub folder, issue the following commands: 
* -> terraform init
* -> terraform apply -var="client_secret=spac_client_secret"  (instead of spac_client_secret, use the password value from az ad sp create-for-rbac command). 

### Provision DevTest spoke
* Copy the private IP of Azure Firewall (open the firewall in Azure Portal and copy the private IP from overview page). 
* Get the subscription ID (id value) and tenant ID (tenantId value) for the devtest subscription (copy from output of command:   **az list**)
* Create a service principal for the DevTest subscription: (**az ad sp create-for-rbac --name=terraform-sp2 --role="Contributor" --scopes="/subscriptions/DEVTEST_SUBSCRIPTION_ID"** )
* copy the values from output of service principal creation.
* Go to the folder terraform/devtest-spoke
* In the block : provider "azurerm" change the values of following, and save the file. 
*  subscription_id => Azure subscription ID (from az list command)
*  -> client_id => appId value from az ad sp create-for-rbac command)
*  -> tenand_id => tenant value from az ad sp create-for-rbac command)
* Change the values in variables.tf
* Change the value of customer-name variable to a short customer identifier value (default = "changeme" to default = "xyz")
* Change the value of firewall-ip with the private IP address of Azure Firewall. 
* From the devtest-spoke folder, issue the following commands: 
* -> terraform init
* -> terraform apply -var="client_secret=spac_client_secret"  (instead of spac_client_secret, use the password value from az ad sp create-for-rbac command). 

Establish VNET peering between both the VNETs
