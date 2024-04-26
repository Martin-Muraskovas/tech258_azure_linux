# Setting up a 2-tier app deployment on Azure
![alt text](/images/image.png)  

- Resource Groups: Organize resources into logical containers for management.
- ARM Templates: Define infrastructure and resource configurations using JSON templates.
- Deployment: Allows deploying and managing resources as a single entity.
- RBAC: Control access to resources with Role-Based Access Control.
- Tags: Organize and track resources using key-value pairs.
- Monitoring and Governance: Monitor, manage, and enforce policies for resources efficiently.

## Setting up an SSH key for Azure

Generate an SSH key in a bash terminal using the following command:

`ssh-keygen -t rsa -b 4096 -C <YOUR_EMAIL>`

To add this ssh key to Azure, search SSH into the search bar and select the SSH option. Proceed to fill out to form. Use the `cat` command in bash to view the content of the public key to use to fill the form out.

![alt text](images/image-1.png)


## Security
In Azure, when you deploy an Ubuntu VM with MongoDB installed, you do not need to open the MongoDB port (default port 27017) explicitly in the network security group. This is because:
- Azure Security Groups are stateful, meaning they allow outbound traffic by default. This allows the VM to communicate outbound without needing to explicitly open ports.
- In Azure, the default behavior is to allow all outbound traffic from a VM, so the VM can initiate connections to external services or resources without additional configuration.
- Inbound traffic to the MongoDB port (27017) is controlled by the MongoDB configuration itself, which by default binds to localhost. This means external access to the MongoDB service is restricted unless configured otherwise within MongoDB itself.
## Setting up a VNet
![alt text](images/image-3.png)<br>
 VNets allow you to divide your network into subnets, which can help organize and segment your resources. For example, you can have separate subnets for different tiers of your application (e.g., front-end, back-end) within the same VNet.
## Basics
Set the settings in your basic section to look as follows:<br>
![alt text](images/image-5.png)

![alt text](images/image-6.png)

![alt text](images/image-7.png)

## Networks
Set your network settings for the app as follows.<br>
![alt text](images/image-11.png)<br>
Set your network settings for the database as follows.<br>
![alt text](images/image-8.png)<br>
When setting up the database instance, do not set up the MongoDB port, this is mangaged for you by Azure.
## Result
![alt text](images/image-12.png)

## Differences Azure and AWS for setting up a 2 tier app and database deployment.
- VNet set-up.<br>
  Azure requires you to set up a VNet while AWS uses VPC(Virtual Private Cloud).

- Subnet set-up.<br>
  For the 2 tier app set up in AWS a subnet was not required, while in Azure it was.

- Azure Static IP<br>
  The public IP on Azure is its own resource. When your instance is started the public IP remains the same. While on AWS it changes every time.

- Security groups.<br>
  Azure uses Network Security Groups (NSGs) to control inbound and outbound traffic while AWS utilizes Security Groups to control traffic at the instance level.

- Tagging names.<br>
  Azure uses tags to categorize resources for organization and management. This was not emphasised in AWS.

- Inbound rules vs Outbound rules<br>
  Due to setting up a VNet, the rules for setting inbound and outbound rules are slightly different. Azure intuitively knows that any instances communicating privately on a virtual netowork are safe. For example in our 2-tier deployment we did not have to configure our MongoDB port due to the VNet handling that for us because it is a private connection that is within our virtual network.