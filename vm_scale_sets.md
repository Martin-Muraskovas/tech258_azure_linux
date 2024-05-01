# VM Scale Sets in Azure
## Creating a VM from an image.
  - Azure Marketplace Images:
    - Azure offers a wide range of pre-built images from the Azure Marketplace that include various operating systems, applications, and development stacks.
  - Custom Images:
    - You can also create custom images by capturing the disk state of an existing VM. 
  
## VM Scale Sets
  - Custom autoscale<br>
    With VM Scale Sets, you can configure custom autoscaling policies based on metrics like CPU utilization or memory usage. This ensures that the number of VM instances automatically adjusts to handle varying workloads efficiently.

  - Availability Zones<br>
    VM Scale Sets can be deployed across Availability Zones in Azure regions. Availability Zones are isolated locations within an Azure region, providing higher availability and fault tolerance. Distributing VM instances across zones helps ensure resilience against failures in a specific zone.


## How do Scale sets and Load balancers make our instances Highly Available and Highly Scalable
- Why our instances are highly available.<br>
Our instances are highly available due to them being avaiable in multiple availability zones. Traffic is directed to different instances by the load balancer. The load balancer can decide to direct traffic to these instances depending on if the availability zone is active, or if the instances running within that zone are healthy.

- Why our instances are highly scalable.<br>
Our instances are highly scalable due to the custom autoscale that we set up when creating our scale sets. The rules of our autoscle dictate that a new instance is to be created when certain parameters are hit. An example of this is if the average CPU usage exceeds 75% then a new instance will be created.



## What do you need to have done and tested before making a scale set?
- A working image
  - In our case, we are deploying an app. Our image should have all dependencies for the application pre installed. In our case we need: our cloned app repo, pm2, nginx, and node.
- Working userdata
  - To run the app from our image, we need a little bit of userdata just to run the app. Here is what this looks like:
    ```
    #!/bin/bash
    cd repo/app

    #npm start

    #should use this instead of npm start
    pm2 kill
    pm2 start app.js 
    ```
- Both your userdata, and your image should work together to run your app on a single VM before you create a VM scale set.

## How to create a scale set on Azure

To begin creating a scale set on Azure, search for "Virtual machine scale sets" in the search bar and select "Virtual machine scale sets".<br>
![](image-27.png)<br>
The approach is very similar to setting up a virtual machine, however there are a few key differences:

Basics<br>
![](image-23.png)<br>

Scaling configuration<br>
![](image-26.png)<br>

Networking<br>
![](image-24.png)<br>

Load Balancer<br>
![](image-22.png)<br>

Advanced - userdata<br>
![](image-25.png)<br>

## How does autoscaling work?

This diagram demonstrates how autoscaling works:<br>
![](<New Project (1) (1).png>)<br>

## What is a load balancer and why it is needed?
  - Balancing availability zones<br>
    Load balancers can distribute traffic evenly across VM instances deployed in different Availability Zones. 

  - Public load balancer<br>
    A public load balancer routes traffic from the internet to the VM instances in a backend pool.

  - Internal facing load balancer<br>
    Balances the load between VM instances serving specific internal applications or services without exposing them to the public internet.
# How to manage the instances within your scale set
- SSH'ing in
  - `ssh -i tech258-martin-az-key -p 50000 adminuser@4.158.77.158`
  - You have to SSH in through the public IP of the load balancer. You also need to add port 50000 to SSH into your first instance.
- Reimaging vs Upgrading
  - Reimaging replaces the OS disk on all instances with a new one. This will essentially start you off with a fresh instance that has the image you selected on it from when you set up your scale set.
  - Upgrading does not change the instance size or the OS disk, but it updates the host environment. This is a method of modifying userdata as you can change your userdata and then upgrade to apply the new userdata.



steps on how to create an unhealthy instance (for testing) and why it is marked as healthy/unhealthy 

steps on how to delete a VM Scale Set and all it's connecting parts









