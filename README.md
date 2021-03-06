![image](https://user-images.githubusercontent.com/61896468/89203924-f0eaf580-d5d2-11ea-9623-f2c6779c40e5.png) 


# Task Description
# Perform the task using EFS service on the AWS as,
# Create/launch Application using Terraform
## 1. Create Security group which allow the port 80.
## 2. Launch EC2 instance.
## 3. In this Ec2 instance use the existing key or provided key and security group which we have created in step 1.
## 4. Launch one Volume using the EFS service and attach it in your vpc, then mount that volume into /var/www/html
## 5. Developer have uploded the code into github repo also the repo has some images.
## 6. Copy the github repo code into /var/www/html
## 7. Create S3 bucket, and copy/deploy the images from github repo into the s3 bucket and change the permission to public readable.
## 8 Create a Cloudfront using s3 bucket(which contains images) and use the Cloudfront URL to update in code in /var/www/html
# Prerequisites-:
## aws account.
## aws CLI installed and added in path.
## Terraform installed and added to the path.
## github account to upload the code.
# Step 1-:
## First we have to create aws account and we have to install aws CLI and terraform and we has to set their paths in Environmental Variables.
## First you have to declare your provider and its neccessary login credentials as shown here for example.-:
```provider "aws" {
  region     = "ap-south-1"
  profile    = "MOHIT"
}
```

# Creating a Security Group-: Next step here is creating a security group which allows the port no. {80 and 22} which provides the services for HTTP protocol and SSH protocol. Egress is not open for all IPs and all ports. Also, CIDR is configured only for IPv4 not IPv6.
```
resource "aws_security_group" "allow_http" {
  name        = "allow_http"
  description = "Allow HTTP SSH inbound traffic"
  vpc_id      = "vpc-d4ebf6bc"


  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }


  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }


  tags = {
    Name = "my_http"
  }
}
```

# Launching the Instance-: This is the most critical step in this whole task as all the processes here are revolving around this. The instance which we are creating here is used to deploy webserver and nearly all other tasks are also done here. This instance is launched using the keys and security groups created previously.
```
resource "aws_instance" "myweb" {
	ami		= "ami-005956c5f0f757d37"
	instance_type	="t2.micro"
	key_name          = "mohit1234"
  	security_groups   = [ "allow_http" ]


	 connection {
    	type        = "ssh"
    	user        = "ec2-user"
    	private_key = file("C:/Users/mohit/Downloads/mohit1234.pem")
    	host        = "${aws_instance.myweb.public_ip}"
  	}
  
 	 provisioner "remote-exec" {
    	inline = [
      	"sudo yum install httpd  -y",
      	"sudo service httpd start",
      	"sudo service httpd enable"
    	]
 	 }


	tags = {
		Name = "mohitos"
	}
}


output "o3" {value = aws_instance.myweb.public_ip
}


output "o4" {
	value = aws_instance.myweb.availability_zone
}
```

# Creating S3 Bucket-: Next, I Created an S3 bucket and also manipulated the terraform to save my bucket name in my local system in my working repository. This is for the time when I destroy the infrastructure created by terraform it will ask for bucket name which I have made dynamic as we need a unique name for our bucket as S3 is a global service.
```
resource "aws_s3_bucket" "myuniquebucket1227" {
  bucket = "myuniquebucket1227" 
  acl    = "public-read"
  tags = {
    Name        = "uniquebucket1227" 
  }
  versioning {
	enabled =true
  }
}


resource "aws_s3_bucket_object" "s3object" {
  bucket = "${aws_s3_bucket.myuniquebucket1227.id}"
  key    = "1076883.jpg"
  source = "C:/Users/mohit/Downloads/1076883.jpg"
}
```

# CloudFront-: Creating Cloudfront distribution for S3 bucket is next step as we wish to decrease latency as much as possible through the CDN (content delivery network). This will provide a different link to all contents of S3 storage and will help in reducing latency for the clients.
```
resource "aws_cloudfront_origin_access_identity" "origin_access_identity" {
  comment = "This is origin access identity"
}


resource "aws_cloudfront_distribution" "imagecf" {
    origin {
        domain_name = "myuniquebucket1227.s3.amazonaws.com"
        origin_id = "S3-myuniquebucket1227"




        s3_origin_config {
      origin_access_identity = aws_cloudfront_origin_access_identity.origin_access_identity.cloudfront_access_identity_path
    }
  }
       
    enabled = true
      is_ipv6_enabled     = true


    default_cache_behavior {
        allowed_methods = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
        cached_methods = ["GET", "HEAD"]
        target_origin_id = "S3-myuniquebucket1227"
```

# Forward all query strings, cookies and headers
 ```
 forwarded_values {
            query_string = false
        
            cookies {
               forward = "none"
            }
        }
        viewer_protocol_policy = "allow-all"
        min_ttl = 0
        default_ttl = 10
        max_ttl = 30
    }
 ```
 
 # Restricts who is able to access this content
```
restrictions {
        geo_restriction {
            # type of restriction, blacklist, whitelist or none
            restriction_type = "none"
        }
    }


```


  # SSL certificate for the service.
```
viewer_certificate {
        cloudfront_default_certificate = true
    }
}
```

# EFS creation-: Creating an extra EFS (Elastic File System) volume. The next step is to attach this extra created volume to our instance so that it can be accessed by us from instance.
``` 
resource “aws_efs_file_system” “efs_plus” {
depends_on = [aws_security_group.mohittf_sg, aws_instance.mohitOs1]
creation_token = “efs”
tags = {
Name = “mohitefs”
}
}
resource “aws_efs_mount_target” “mount_efs” {depends_on = [aws_efs_file_system.efs_plus]
file_system_id = aws_efs_file_system.efs_plus.id
subnet_id = aws_instance.mohitos.subnet_id
security_groups=[aws_security_group.mohittf_sg.id]
}
resource “null_resource” “cluster” {
depends_on = [
aws_efs_file_system.efs_plus,
]
connection {
type = “ssh”
user = “ec2-user”
private_key = file("C:/Users/mohit/Downloads/mohit1234.pem")
host = aws_instance.mohitos.public_ip
}
provisioner “remote-exec” {
inline = [“sudo echo ${aws_efs_file_system.efs_plus.dns_name}:/var/www/html efs defaults._netdev 0 0>>sudo /etc/fstab”,
“sudo mount ${aws_efs_file_system.efs_plus.dns_name}:/var/www/html/*”,
“sudo rm -rf /var/www/html/*”,
“sudo git clone https://github.com/aniketambasta/aws_task_2 /var/www/html “
   ]
  }
}
```

![image](https://user-images.githubusercontent.com/61896468/97106647-0ad73880-16e9-11eb-934a-1fc7c12d900c.png)

![image](https://user-images.githubusercontent.com/61896468/97106658-13c80a00-16e9-11eb-939a-4326a51db604.png)

![image](https://user-images.githubusercontent.com/61896468/97106666-1a568180-16e9-11eb-8643-223dbf0d28df.png)

![image](https://user-images.githubusercontent.com/61896468/97106675-25111680-16e9-11eb-8131-8dbd6d3af9ac.png)

![image](https://user-images.githubusercontent.com/61896468/97106685-2b9f8e00-16e9-11eb-9636-c355acc2b6a9.png)

![image](https://user-images.githubusercontent.com/61896468/97106693-30fcd880-16e9-11eb-976a-eb847763a647.png)




























# PROJECT COMPLETED...

# Thank You vimal sir and all volunteers working 24*7 for all your mesmerizing support.... 
