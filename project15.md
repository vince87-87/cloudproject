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
