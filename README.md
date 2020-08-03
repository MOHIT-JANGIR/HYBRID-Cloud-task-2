![image](https://user-images.githubusercontent.com/61896468/89203924-f0eaf580-d5d2-11ea-9623-f2c6779c40e5.png) 
# Building AWS infrastructure with EFS using Terraform

![image](https://user-images.githubusercontent.com/61896468/89204268-7373b500-d5d3-11ea-87e4-35373ae5f138.png)

 

# Amazon Elastic File System (Amazon EFS) :

#### EFS:- Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic NFS file system for use with AWS Cloud services and on-premises resources. It provides centralized storage and we can able to connect it to multiple instances that are not in case of EBS.

# Tasks to Perform:

# Perform the Task-1 using EFS instead of EBS service on the AWS as

# Create/launch Application using Terraform

# 1. Create a Security group that allows the port 80.

# 2. Launch EC2 instance.

# 3. In this Ec2 instance use the existing key or provided key and security group which we have created in step 1.

# 4. Launch one Volume using the EFS service and attach it in your vpc, then mount that volume into /var/www/html

# 5. A developer has uploaded the code into GitHub repo also the repo has some images.

# 6. Copy the github repo code into /var/www/html

# 7. Create S3 bucket, and copy/deploy the images from github repo into the s3 bucket and change the permission to public readable.

# 8 Create a Cloudfront using s3 bucket(which contains images) and use the Cloudfront URL to update in code in /var/www/html

### Let's Start:

Suppose the developer has uploaded the webpage code on Github and I am going to clone or download it automatically and deploy it to the working directory of my web server on the ec2 instance. My objective with the approach is mentioned below with code:-

### Create a new folder or directory where you want and save all the below files in that folder/directory.

```
terraform init
```

![image](https://user-images.githubusercontent.com/61896468/89204326-8a1a0c00-d5d3-11ea-973a-b0936a483e84.png) 

### Create an IAM user in the AWS account and configure a profile in cmd using a secret and access key. Initialization and other settings are:-

```
aws configure --profile Mohit
```


![image](https://user-images.githubusercontent.com/61896468/89204398-a7e77100-d5d3-11ea-9a16-02ca242b906d.png) 

For launching the application on AWS cloud a need is O.S. so for that ec2 instance is there in AWS, but os needs a hard disk so instead of block storage attachment attaching elastic file storage(EFS) is the best practice and for storing static data S3 Bucket is there in AWS, to load images or static data quick or with less latency Cloud Front is there in AWS, for the security purpose of instance, need to create security group of AWS where port 22 is open for ssh login, but for logging through ssh we need a private key called key pair in AWS, so anyone having key pair and public IP of ec2 instance can log in from any outside o.s.

First, a challenge is to upload our static data or images on s3 bucket and attach cloud front to s3 bucket, so by using the cloud front URL we can update our code for images(src/path) and also any other static data code.

# To create the AWS s3 bucket and cloud front save following code as cp.tf

![image](https://user-images.githubusercontent.com/61896468/89204446-bf265e80-d5d3-11ea-995b-daa7519134f5.png)
![image](https://user-images.githubusercontent.com/61896468/89204464-c77e9980-d5d3-11ea-9a0a-de56b4ee0f9c.png)
![image](https://user-images.githubusercontent.com/61896468/89204483-cf3e3e00-d5d3-11ea-86a6-2fe25a546f8c.png)
![image](https://user-images.githubusercontent.com/61896468/89204505-d7967900-d5d3-11ea-8495-18cefe0801b9.png)
![image](https://user-images.githubusercontent.com/61896468/89204524-e0874a80-d5d3-11ea-8275-90f84827dab3.png)
![image](https://user-images.githubusercontent.com/61896468/89204546-e7ae5880-d5d3-11ea-83ae-898b2ac21e45.png)


#### Then Run Command:-

```
terraform apply -auto-approve
```

![image](https://user-images.githubusercontent.com/61896468/89204617-04e32700-d5d4-11ea-8da6-c2bd68b88350.png) 

You or your team have to use that URL to update your source code on GitHub or at any place where your code is located.

So now the challenge is to launch os, create a security group, create EFS, attach EFS to ec2 instance and make a ready os for launching the application for that save the below code with task-2.tf .

# Note: Your git repo must include the index.html/index.php file because here we are cloning the repo to /var/www/html directory.

![image](https://user-images.githubusercontent.com/61896468/89204666-1c221480-d5d4-11ea-9c98-65e4c63007ef.png)
![image](https://user-images.githubusercontent.com/61896468/89204687-2512e600-d5d4-11ea-8bf7-3c2a9eb11540.png)
![image](https://user-images.githubusercontent.com/61896468/89204708-2d6b2100-d5d4-11ea-9f70-d24b85c1ea77.png)
![image](https://user-images.githubusercontent.com/61896468/89204732-36f48900-d5d4-11ea-9583-2307b664ba69.png)
![image](https://user-images.githubusercontent.com/61896468/89204758-407df100-d5d4-11ea-957f-a4feda6caf52.png)
![image](https://user-images.githubusercontent.com/61896468/89204783-4a9fef80-d5d4-11ea-9ec8-cdbfd7ec584b.png)
![image](https://user-images.githubusercontent.com/61896468/89204799-512e6700-d5d4-11ea-89c0-dd5419d5638c.png)


Run above code with command as shown in the image .

```
"terraform apply -auto-approve"
```


![image](https://user-images.githubusercontent.com/61896468/89204871-702cf900-d5d4-11ea-8a16-9ad868514391.png)
![image](https://user-images.githubusercontent.com/61896468/89204891-76bb7080-d5d4-11ea-92d4-973ddeb05e98.png)
![image](https://user-images.githubusercontent.com/61896468/89204915-8044d880-d5d4-11ea-83f1-ab7bf14c7e4c.png)
![image](https://user-images.githubusercontent.com/61896468/89204939-8935aa00-d5d4-11ea-90cd-b8b2cd3948dc.png)
![image](https://user-images.githubusercontent.com/61896468/89204977-95ba0280-d5d4-11ea-96c9-0bfe92e0e978.png)
![image](https://user-images.githubusercontent.com/61896468/89204985-9bafe380-d5d4-11ea-8f5c-e68d96badbcc.png)




We will create this Jenkins job which will download code from GitHub and run it.



![image](https://user-images.githubusercontent.com/61896468/89205178-f0535e80-d5d4-11ea-91a2-f2b88d125c6f.png) 




### In Source Code Management we will use git as we have pushed our code to GitHub. Here we’ll specify the GitHub repository from which we want to download our code.

![image](https://user-images.githubusercontent.com/61896468/89205232-07924c00-d5d5-11ea-85d3-bc7e89474280.png) 

After downloading the code, to create our Infrastructure we have to apply our terraform code. For this, we will Execute shell command and apply our terraform code.

![image](https://user-images.githubusercontent.com/61896468/89205292-1f69d000-d5d5-11ea-8f41-b931b1495636.png) 

But as soon as the developer build job Infrastructure Creation will start automatically.

![image](https://user-images.githubusercontent.com/61896468/89205338-33adcd00-d5d5-11ea-83c5-c7bc62bac99d.png) 

##### Final Output of the project:

![image](https://user-images.githubusercontent.com/61896468/89205393-4a542400-d5d5-11ea-9f69-cbb4ca65bb54.png) 

# PROJECT COMPLETED...

# Thank You vimal sir and all volunteers working 24*7 for all your mesmerizing support.... 
