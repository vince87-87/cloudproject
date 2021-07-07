# AWS Cloud Solution For 2 Company Websites Using A Reverse Proxy Technology
# Prerequsiste

Create an AWS Master account. (Also known as Root Account)

Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)

Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there)
Move the DevOps account into the Dev OU.

![image](https://user-images.githubusercontent.com/49937302/124681956-45da6e80-defc-11eb-856d-f21e00e18b95.png)

Login to the newly created AWS account using the new email address.

Using existing domain or Create a free domain name for your fictitious company.

Create a hosted zone in AWS, and map it to your domain

![image](https://user-images.githubusercontent.com/49937302/124682911-86d38280-defe-11eb-978e-0f53ea4d3736.png)

![image](https://user-images.githubusercontent.com/49937302/124682952-9eab0680-defe-11eb-9372-4d5f757bbc11.png)

# Set Up a Virtual Private Network (VPC)

Setup VPC

![image](https://user-images.githubusercontent.com/49937302/124685119-31e63b00-df03-11eb-8359-4a0cb70f18d0.png)

Setup subnet

![image](https://user-images.githubusercontent.com/49937302/124685073-1aa74d80-df03-11eb-80fe-a72fbe2b5181.png)

Create internet gateway and attached to the new VPC

![image](https://user-images.githubusercontent.com/49937302/124689577-53e3bb80-df0b-11eb-8a60-ab205cfb9a46.png)

Create 2 route table for public and private, associate public subnet to public route table and web/db subnet to the private route table

![image](https://user-images.githubusercontent.com/49937302/124689612-64943180-df0b-11eb-9757-a649d3db6627.png)

![image](https://user-images.githubusercontent.com/49937302/124689678-7e357900-df0b-11eb-93b0-be92c3c56b41.png)

Create Elastic ip & associate with NAT

Create NAT Gateway

#Create Security group for the below

NGINX: allow port 80 from ALB Security group

Bastion Servers: Allow port 22 from workstation public ip

Ext_ALB: Allow port 80 from anywhere

Int_ALB: allow port 80 from nginx

Webservers: allow port 80 from int alb

DB: allow 1433 from web servers

EFS: allow from nginx & web servers

![image](https://user-images.githubusercontent.com/49937302/124693320-cb1c4e00-df11-11eb-9464-4f055e13ad75.png)

# Setup Compute Resources for nginx

Provision ec2 instance using centos ami and install/update related software


sudo yum install ntp python3 net-tools vim wget telnet epel-release-y

sudo yum install htop -y

sudo yum update -y

![image](https://user-images.githubusercontent.com/49937302/124697638-b93ea900-df19-11eb-8dcf-d0b9c9e2c2be.png)
