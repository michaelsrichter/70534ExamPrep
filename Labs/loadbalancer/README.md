# Azure Load Balancer Lab

### Summary

In this lab, you will create a load balancer a and a highly available Azure VM based solution. You will use Azure Powershell, so make sure it is installed. You can follow the directions to install [Azure Powershell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0) or you can use the Web Platform Installer ([instructions here](https://docs.microsoft.com/en-us/powershell/azure/other-install?view=azurermps-4.0.0)).

This Lab will show you how to do deploy the following from the Command Line:

* Create an Azure load balancer
* Create a load balancer health probe
* Create load balancer traffic rules
* Use the Custom Script Extension to create a basic IIS site
* Create virtual machines and attach to a load balancer
* View a load balancer in action
* Add and remove VMs from a load balancer

### Lab Steps

1. Follow the steps [here](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-load-balancer).

After the lab, 

* Visit your solution resources in the Azure portal.
* Notice the availability set and the Fault Domain and Upgrade Domain for each of your VMs.