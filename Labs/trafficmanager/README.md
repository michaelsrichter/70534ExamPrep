# Azure Load Balancer Lab

### Summary

In this lab, you use a community built Azure quickstart ARM template to deploy a Traffic Manager and two VM workloads in different Azure Regions. The VMs are configured automatically with a PHP application so you can see which region you are hitting when you make requests to the application. 

Traffic Manager will be configured with the "Weighted" pattern and both endpoints will have an equal weight. The DNS call is cached for 30 seconds, so every 30 seconds there's a 50/50 chance either region will respond to your request. 

### Lab Steps

1. Go to this Azure Quickstart repo for "Azure Traffic Manager Demo Setup":
[https://github.com/Azure/azure-quickstart-templates/tree/master/traffic-manager-demo-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/traffic-manager-demo-setup)
2. Click on the blue "Deploy to Azure" button
3. You are redirected to the Azure Portal, you may be asked to sign in.
4. This page asks you to provide the necessary info and parameters for deploying this ARM template.
5. Create a new Resource Group called "trafficmanagerdemo"
6. Choose a Resource Group location.
7. Choose a unique DNS name for Traffic Manager. This name needs to be globally unique. So try something unique like *70534yournameyourbirthday*.
8. For Primary and Seconday Location, leave the defaults or choose 2 other different locations each. In production, these should be region pairs, like East US and West US.
9. Leave the routing method as Weighted. 
10. Add Username and Passwords. You will not be logging into these machines, so these values aren't that important.
11. Leave artifacts location.
12. Agree to the terms.
13. Pin to dashboard (so we can find it later).
14. Click Purchase (note you are not buying anything beyond the cost of setting up these servers. We can delete the Resource Group when we are done, so running Template should only cost pennies for an hour).
15. On the Azure dashboard you will see a box that says "Deploying Template" with some animation. Click on it and see what's happening. 
16. While we are waiting, check out some interesting parts of the template:
     1. The main template calls a nested template twice to deploy 2 web servers. See [here](https://github.com/Azure/azure-quickstart-templates/blob/master/traffic-manager-demo-setup/azuredeploy.json#L123).
     2. The nested template calls a Custom Script extension to configure the web servers. See [here](https://github.com/Azure/azure-quickstart-templates/blob/master/traffic-manager-demo-setup/nested/azuredeploywebserver.json#L336).
     3. The script that the nested template calls is [here](https://github.com/Azure/azure-quickstart-templates/blob/master/traffic-manager-demo-setup/nested/scripts/prepwebserver.sh).
### After the Lab

* When the deployment is over, you can hit your traffic manager by typing the dns name you provided in the setup followed by .trafficmanager in a browser (for instance: *http://70534yournameyourbirthday.trafficmanager.com*).
* Wait 30 seconds and try refreshing. Or, open multiple browsers and try refreshing. You will eventually see that both sites have responded to your request!
* Delete the resource group when you are finished to free up resources and to stop paying.
 
