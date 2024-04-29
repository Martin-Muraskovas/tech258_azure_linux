# Using User Data to set up a 2-tier deployment on Azure

## About userdata
- Runs as root user (i.e. every command will be run as sudo whether you've put sudo in the command or not).
  
- Starting file location is `/`.
  
- Only runs once immediately after VM is created.

## Setting up a 2-tier deployment
Set up your VM using this [guide](https://github.com/Martin-Muraskovas/tech258_azure_linux/blob/main/2-tier-azure-deployment.md). However before deploying, go to the advanced section and do the following. 

### userdata for the database.
![alt text](image.png)<br>
SSH in to validate that userdata has worked.<br>
![alt text](image-2.png)


### userdata for the app.
![alt text](image-4.png)<br>
Check the Public IP to ensure the userdata has worked.<br>
![alt text](image-3.png)<br>
View public IP of app instance to validate that userdata has worked.<br>

## Creating an Image
Follow the creating a Virtual Machine [guide](https://github.com/Martin-Muraskovas/tech258_azure_linux/blob/main/2-tier-azure-deployment.md). Set up the environment within the virtual machine so that all of the dependencies are installed.<br>

This is the script we will pass as userdata to create a VM that we will take a snapshot of and save as an Image:<br>
```
#!/bin/bash

sudo DEBIAN_FRONTEND=noninteractive apt update -y

sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y

sudo DEBIAN_FRONTEND=noninteractive apt install nginx -y

sudo sed -i '51s/.*/\t        proxy_pass http:\/\/localhost:3000;/' /etc/nginx/sites-available/default

sudo systemctl restart nginx

sudo systemctl enable nginx

curl -fsSL https://deb.nodesource.com/setup_20.x | sudo DEBIAN_FRONTEND=noninteractive -E bash - &&\
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs

mkdir ~/apps
cd ~/apps

git clone https://github.com/Martin-Muraskovas/tech258-sparta-test-app.git
```
<br> In our case we are downloading and installing nginx and node on our system. We are not yet loading the app. It is at this point that we create an image.

Use this button to begin creating an image of your VM:
![alt text](image-6.png)<br>

Once we have created an image. We can create a new VM based on that image. When creating a VM from our image we will pass this script as userdata. This script essentially just launches the application:

```
#!/bin/bash

cd ~/apps/tech258-sparta-test-app/app

export DB_HOST=mongodb://10.0.3.5:27017/posts

sudo -E npm install

npm start
```

## Deploying from an image.

![alt text](image-7.png)
Deploying from an image is the same process as deploying a regular VM, the only exception is that you will be deploying from an image that you have created rather than a prepackaged image from Azure. You can refer to this [guide](https://github.com/Martin-Muraskovas/tech258_azure_linux/blob/main/2-tier-azure-deployment.md) to streamline the process, also you may want to refer to the userdata section of this document to automate tasks like running an application or launching a database.