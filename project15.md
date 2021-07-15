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

![image](https://user-images.githubusercontent.com/49937302/125006691-4c4e1f00-e091-11eb-9a76-f857c398ef92.png)

![image](https://user-images.githubusercontent.com/49937302/124689678-7e357900-df0b-11eb-93b0-be92c3c56b41.png)

![image](https://user-images.githubusercontent.com/49937302/125006633-26c11580-e091-11eb-97ec-51a6253a6842.png)

Create Elastic ip & associate with NAT

![image](https://user-images.githubusercontent.com/49937302/125006472-ccc05000-e090-11eb-9ac2-1d87331783ae.png)

Create NAT Gateway

![image](https://user-images.githubusercontent.com/49937302/125006415-a7cbdd00-e090-11eb-93b9-43ee1a37a810.png)

#Create Security group for the below

NGINX: allow port 80 from ALB Security group

Bastion Servers: Allow port 22 from workstation public ip

Ext_ALB: Allow port 80 from anywhere

Int_ALB: allow port 80 from nginx

Webservers: allow port 80 from int alb

DB: allow 3306 from web servers

EFS: allow from nginx & web servers

![image](https://user-images.githubusercontent.com/49937302/124693320-cb1c4e00-df11-11eb-9464-4f055e13ad75.png)

# Setup Compute Resources for nginx

Provision ec2 instance using centos ami and install/update related software


sudo yum install ntp python3 net-tools vim wget telnet epel-release-y

sudo yum install htop -y

sudo yum update -y

![image](https://user-images.githubusercontent.com/49937302/124697638-b93ea900-df19-11eb-8dcf-d0b9c9e2c2be.png)

Create ami

![image](https://user-images.githubusercontent.com/49937302/124698327-39b1d980-df1b-11eb-970e-385b6a2003aa.png)

# Prepare Launch Template For Nginx (One Per Subnet)

Assign backup ami, tag, nginix security group & configure user data

#!/bin/bash

sudo yum update -y

sudo yum install nginx -y

sudo systemctl start nginx

sudo systemctl enable nginx

![image](https://user-images.githubusercontent.com/49937302/124706191-74bb0980-df29-11eb-9f09-23a6d7579326.png)


Launch NGINX from template from each zone

![image](https://user-images.githubusercontent.com/49937302/124706107-5c4aef00-df29-11eb-828f-c13f88a1b5d3.png)

![image](https://user-images.githubusercontent.com/49937302/124706032-3aea0300-df29-11eb-8b1b-d75c0d77b42b.png)

# Configure ALB target group

Select Instances as the target type

Ensure the protocol HTTPS on secure TLS port 443

Ensure that the health check path is /healthstatus

Register Nginx Instances as targets

Ensure that health check passes for the target group

![image](https://user-images.githubusercontent.com/49937302/124722739-ed779100-df3c-11eb-9667-f0c75b37e68e.png)

# Configure Autoscaling For Nginx

Select the right launch template

Select the VPC

Select both public subnets

Enable Application Load Balancer for the AutoScalingGroup (ASG)

Select the target group you created before

Ensure that you have health checks for both EC2 and ALB

The desired capacity is 2

Minimum capacity is 2

Maximum capacity is 4

Set scale out if CPU utilization reaches 90%

Ensure there is an SNS topic to send scaling notifications

![image](https://user-images.githubusercontent.com/49937302/124733746-11d86b00-df47-11eb-8308-35ba865fb2f9.png)

![image](https://user-images.githubusercontent.com/49937302/124733823-24eb3b00-df47-11eb-942c-d3ae053c5ab4.png)

# Set Up Compute Resources for Bastion

# Provision the EC2 Instances for Bastion

Provision bastion host via centos ami and install basic tools with user data

#!/bin/bash

sudo yum install ntp python3 net-tools vim wget telnet epel-release -y

sudo yum install htop -y

sudo yum update -y

Create ami image

![image](https://user-images.githubusercontent.com/49937302/124761270-33941b00-df64-11eb-84ce-996a3e98e2d4.png)

# Prepare Launch Template For Bastion (One per subnet)

Assign Bastion-template backup ami, tag, bastion security group & configure user data

#!/bin/bash

sudo yum install git ansible -y

sudo yum update -y

![image](https://user-images.githubusercontent.com/49937302/124762549-976b1380-df65-11eb-9d6e-fca099822fbf.png)

# Configure Target group

# Configure AutoScaling For Bastion

Select the right launch template

Select the VPC

Select both public subnets

Configure instance for the AutoScalingGroup (ASG)

The desired capacity is 2

Minimum capacity is 2

Maximum capacity is 4

Set scale out if CPU utilization reaches 90%

Ensure there is an SNS topic to send scaling notifications

![image](https://user-images.githubusercontent.com/49937302/124774015-0a798780-df70-11eb-9cd7-cad74f238723.png)

![image](https://user-images.githubusercontent.com/49937302/124773695-c1293800-df6f-11eb-8f2f-1c000079e23d.png)

![image](https://user-images.githubusercontent.com/49937302/124777954-27638a00-df73-11eb-84ed-eb6e7972c4d4.png)

# Set Up Compute Resources for Webservers

# Provision the EC2 Instances for Webservers

Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).

Create an AMI out of the EC2 instance

![image](https://user-images.githubusercontent.com/49937302/124871512-b5cd1f80-dff6-11eb-8d0b-ab9f58b59832.png)

# Configure launch template for web servers

Assign Bastion-template backup ami, tag, bastion security group & configure user data

Configure Userdata to update yum package repository and install wordpress (Only required on the WordPress launch template)

![image](https://user-images.githubusercontent.com/49937302/124871992-4efc3600-dff7-11eb-8755-d3c03a69a49b.png)

# TLS Certificates From Amazon Certificate Manager (ACM)

Navigate to AWS ACM

Request a public wildcard certificate for the domain name you register

Use DNS to validate the domain name

Tag the resource

![image](https://user-images.githubusercontent.com/49937302/124873533-2d03b300-dff9-11eb-8bcb-9f0b59db4725.png)

# Configure Application Load Balancer (ALB)

# Application Load Balancer To Route Traffic To NGINX

Create an Internal ALB

Ensure that it listens on HTTPS protocol (TCP port 443)

Ensure the ALB is created within the appropriate VPC | AZ | Subnets

Choose the Certificate from ACM

Select Security Group

Select webserver Instances as the target group

Ensure that health check passes for the target group

# Setup EFS

Create an EFS filesystem

Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer

Associate the Security groups created earlier for data layer.

Create an EFS access point. (Give it a name and leave all other settings as default)

![image](https://user-images.githubusercontent.com/49937302/125006857-a7801180-e091-11eb-9881-73cc2ec16a2d.png)

![image](https://user-images.githubusercontent.com/49937302/125148889-04e69200-e168-11eb-8fb5-14c098f2f1d0.png)

# Configure EFS on web servers

# Install EFS Client

yum -y install git

git clone https://github.com/aws/efs-utils

cd efs-utils

yum -y install rpm-build

yum -y install make

yum -y install rpm-build

make rpm

yum -y install ./build/amazon-efs-utils*rpm

https://docs.aws.amazon.com/efs/latest/ug/installing-amazon-efs-utils.html#installing-other-distro

**Note: The amazon-efs-utils package comes with Amazon Linux and Amazon Linux AMIS, and is available for installation on EC2 instances running these AMIs.

# Blocker:

If encounter the below error:

![image](https://user-images.githubusercontent.com/49937302/125017661-2f701680-e0a6-11eb-8d4d-0fc7ac9adfde.png)

vi /etc/amazon/efs/efs-utils.conf

set from true to false

stunnel_check_cert_hostname = false

![image](https://user-images.githubusercontent.com/49937302/125018016-d48aef00-e0a6-11eb-87ff-79bb3ad6b700.png)

# mount the access point

sudo mount -t efs -o tls,accesspoint=fsap-0cb6dac604b88ba52 fs-41259001 /mnt/wp

sudo mount -t efs -o tls,accesspoint=fsap-0ebf720bab933381e fs-41259001 /mnt/lb

sudo vi /etc/fstab

fs-41259001.efs.ap-southeast-1.amazonaws.com:/ /mnt/wp efs _netdev,tls,accesspoint=fsap-0cb6dac604b88ba52 0 0

![image](https://user-images.githubusercontent.com/49937302/125033782-56d4dc80-e0c2-11eb-87fa-cc3bb6113bec.png)

# Setup RDS

Pre-requisite: Create a KMS key from Key Management Service (KMS) to be used to encrypt the database instance.

![image](https://user-images.githubusercontent.com/49937302/125041371-4412d580-e0cb-11eb-9474-782f6df19e72.png)

Create a subnet group and add 2 private subnets (data Layer)

Create an RDS Instance for mysql 8.*.*

To satisfy our architectural diagram, you will need to select either Dev/Test or Production Sample Template. But to minimize AWS cost, you can select the Do not create a standby instance option under Availability & durability sample template (The production template will enable Multi-AZ deployment)

Configure other settings accordingly (For test purposes, most of the default settings are good to go). In the real world, you will need to size the database appropriately. You will need to get some information about the usage. If it is a highly transactional database that grows at 10GB weekly, you must bear that in mind while configuring the initial storage allocation, storage autoscaling, and maximum storage threshold.

Configure VPC and security (ensure the database is not available from the Internet)

Configure backups and retention

Encrypt the database using the KMS key created earlier

Enable CloudWatch monitoring and export Error and Slow Query logs (for production, also include Audit)

![image](https://user-images.githubusercontent.com/49937302/125076526-9c110280-e0f2-11eb-8df6-2705131d8385.png)

configure config to point it rds dns

![image](https://user-images.githubusercontent.com/49937302/125299960-30ca6900-e35c-11eb-99f4-4d658f321034.png)

Verify connection to db is working

![image](https://user-images.githubusercontent.com/49937302/125299264-9407cb80-e35b-11eb-907b-8d01ae0a9d27.png)

Configure external alb and internal alb

![image](https://user-images.githubusercontent.com/49937302/125299417-b699e480-e35b-11eb-9546-65d2a3e66875.png)

Configure alias record for route 53 and point to alb

![image](https://user-images.githubusercontent.com/49937302/125299597-df21de80-e35b-11eb-83d9-6a6f7e2aa6dd.png)

Verify wordpress can be accessible

![image](https://user-images.githubusercontent.com/49937302/125719884-d81d1ba0-c17f-41ab-8d3d-3c68ef4d45b3.png)
