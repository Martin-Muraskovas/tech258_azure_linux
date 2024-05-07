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
  - In our case, we are deploying an app. Our image should have all dependencies for the application pre installed. In our case we need: our cloned app repo, pm2, nginx, and node.js.
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
![](<images 2/image-27.png>)<br>
The approach is very similar to setting up a virtual machine, however there are a few key differences:

Basics<br>
In our basics section, we need to include the availability zones that we would like our machines to be deployed on. We also need to select a uniform orchestration mode.<br>
![](<images 2/image-23.png>)<br>
Our scaling mode should be set to autoscaling. To use autoscaling we need to set up a custom autoscale:<br>
![](<images 2/image-26.png>)<br>
Our scale set will also require a custom NIC, this is located on the networking tab:<br>
![](<images 2/image-24.png>)<br>
Our load balancer is used to direct traffic from port 80 to the most suitable VM instance. We have additionally set it up to direct SSH traffic on port 22, to ports beginning at 50000. For example, to access our first VM we would connect to port 50000, but to access our second port we would connect to port 50001.<br>
![](<images 2/image-22.png>)<br>
Each instance that we will be running will require a little bit of userdata. This specific situation requires us to launch our app, we use userdata to do this because you are not able to have the running app be captured in an Image.<br>
![](<images 2/image-25.png>)<br>

## How does autoscaling work?

This diagram demonstrates how autoscaling works:<br>
![](<images 2/New Project (1) (1).png>)<br>

## What is a load balancer and why it is needed?
  - Balancing availability zones<br>
    Load balancers can distribute traffic evenly across VM instances deployed in different Availability Zones. 

  - Public load balancer<br>
    A public load balancer routes traffic from the internet to the VM instances in a backend pool.

  - Internal facing load balancer<br>
    Balances the load between VM instances serving specific internal applications or services without exposing them to the public internet.
## How to manage the instances within your scale set
- SSH'ing in
  - `ssh -i tech258-martin-az-key -p 50000 adminuser@4.158.77.158`
  - You have to SSH in through the public IP of the load balancer. You also need to add port 50000 to SSH into your first instance.
  
- Reimaging vs Upgrading

  - Reimaging replaces the OS disk on all instances with a new one. This will essentially start you off with a fresh instance that has the image you selected on it from when you set up your scale set.

  - Upgrading does not change the instance size or the OS disk, but it updates the host environment. This is a method of modifying userdata as you can change your userdata and then upgrade to apply the new userdata.

## How to create an unhealthy instance.
If you stop one of the running instances and then start it again. It will not run the userdata. This creates an unhealthy instance. If it remains unhealthy for 10 minutes then the instance will be terminated and the autoscaler will create a new one as there will be less than the minimum number of instances running.

## How to delete a VM scale set.
I have found that the easiest way to delete a VM scale set is to go into the resource group and delete these three things:<br>
- The load balancer.
- The public IP address.
- The VM scale set.<br>
Remember to use a conventional naming method so that you can easily locate these things when trying to delete them.









