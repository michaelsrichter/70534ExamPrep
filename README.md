Read Me

# Azure VNET-to-VNET Connection Lab

## Lab Steps

### Set up

1. Go to https://portal.azure.com and log in. 
2. Open the Cloud Shell by clicking the >_ symbol in the top right nav bar.
3. You may be asked to create a Storage container so you can save your work between sessions. Say YES and continue. Don't worry, this will cost you fractions of pennies.
4. When the shell loads, we'll go into our clouddrive so that anything we create will be saved between sessions.
5. Type `cd clouddrive`
6. Type `az group create -n examprep101 -l eastus` | This will create a new Resource Group in the East US. A resource group is a logical container that houses our resources in Azure. We can assign user or group permissions to individual resources or an entire resource group. All the resources we create in this step will live in resource group "examprep101." The Resource Group records will be stored in our "East US" Region datacenters. You can deploy resources to any of our regions inside the resource group, even though the records are hosted in East US. East US just holds the records for the resource group, not the resources themselves. As you will see, we will deploy resources that are deployed to both East US and our WEST US regions.
7. If you did the previous step correctly, you will see this. If not, try again. :(
8. Let's get the template we are going to deploy. Type: `wget https://raw.githubusercontent.com/michaelsrichter/70534ExamPrep/master/template.json` the Azure template file will download!
9. We are going to deploy this template. It will do all the magic for us. Run the following command. It will take while, so I will explain what is happening afterwards. 
Type: `az group deployment create -n mydeployment -g examprep101 --template-file template.json --no-wait`

Great, we are done with setup!

### Now what?
OK, what did that deployment do? It created a new deployment of resources to Azure. We called it "mydeployment." We are deploying to our new resource group called "examprep101" and the resources we deployed are in the template.json file we just downloaded. 

Some of the resources we are provisioning can take a while, namely our vnet Gateways. We can see the status of our deployment by typing `az group deployment show -n mydeployment -g examprep101 | grep provisioning`

If it says `Running` then we know it's not finished yet.

What resources did we create? You can look inside the template.json file, but you can get a list from Azure as well: Type `az resource list -g examprep101 -o table`
* Notice the **Type** column. You see virtualMachines, disks and networkInterface types. These are for our VMs that we will log into. 
* Notice that one set of VM resources is in East US and the other set is in West US.
* You see we have 2 virtualNetwork resources as well. Also, one in the East and one in the West. Each VM is joined to each vnet via the networkInterface attached.
* Let's investigate the address spaces for these networks. Type
```
az network vnet show -n EastUSNetwork -g examprep101 | grep addressPrefix
az network vnet show -n WestUSNetwork -g examprep101 | grep addressPrefix
```
* The networkInterface is where public and private IP addresses are configured.
* We've also got 2 Network Security Groups. Let's check out the first one, type `az network nsg rule list --nsg-name jumpbox-nsg -g examprep101 -o table`
You'll see there is one rule defined. It allows inbount traffic on port 22 from any source (notice SourceAddressPrefix is *), including the internet. This rule allows us to SSH into our jumpbox. 
* Let's look at the remotebox nsg. Type `az network nsg rule list --nsg-name jumpbox-nsg -g examprep101 -o table` - this one is a little different. It only allows traffic from 10.0.0.0/24. Hey, that's the IP address range of our default subnet in our East US VNET! As you can see, the only way to SSH into our remotebox is from a machine that comes from that IP address range.
* We've got two virtualNetworkGateway resources. These are gateways that we can use to connect VPNs into our vnets. In this lab, we are not VPNing from somewhere external into Azure. We are connecting to Azure VNETs in separate regions via these gateways. When we connect 2 Azure vnets across regions, the connection must go through these external-facing gateways. If the vnets were in the same region, they can be easily peered via Azure's internal backbone. 
* We've got 3 Public IP Addresses. 1 is assigned to our jumpbox so we can SSH into it. The other 2 Public IP Addresses are for the gateways so they can interact externally (but in this case, they are going to interact with each other across East US and West US regions).
* Let's find more info about jumpbox IP address so we can connect to it. Type `az network public-ip show -g examprep101 -n jumpbox-ip -o table`
* Note the IP Address, we will use that to connect to our jumpbox.
* We have a remotebox in the West US. There's no Public IP Address. How do we get there?
* Our jumpbox is in the East US. We will use it to connect to our remotebox in the West US. Once we are on our jumpbox, we will SSH into our remotebox via our vnet-to-vnet connection!
* How will we find the remotebox? We need its private IP address. Type `az network nic ip-config list -g examprep101 --nic-name remoteboxnic -o table`
* Note the private IP Address.
* Let's try it. Type `ssh azure@<JUMPBOX-PUBLIC-IP-ADDRESS>` - say, yes, you are sure you want to continue. Paste in the password: `St@lllaBlu3s`
* Great you are on the jumpbox!
* Now, from inside the jumpbox, try reaching the remotebox. Type `ssh azure@<REMOTEBOX-PRIVATE-IP-ADDRESS>` and enter the same password.
* Two things could have happened, if you are prompted for the password... congratulations, it worked! You successfully used a vnet-to-vnet connection to: SSH into the jumpbox in the East US and use a private IP address to SSH into a remote box that is not exposed to the internet and in a completely separate network thousands of miles away.
* if this did NOT happen. There are a few reasons.
  * You are super quick and the Gateways (which can 30+ minutes to provision) aren't ready yet. See if your deployment is still running with `az group deployment show -n mydeployment -g examprep101 | grep provisioning` (remember to `exit` if you are SSH'd into the jumpbox before running this example). If it says `Running` wait a few more minutes for it say `Succeeded`
  * If you get a failure or you are not able to SSH into either machine, let me know by raising an issue here: https://github.com/michaelsrichter/70534ExamPrep/issues



