# AWS-for-DevOps-Engineers

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)

![](AWS.png)  

## Exercises

### IAM

<details>
<summary><b><i>1.Create a User
  
As you probably know at this point, it's not recommended to work with the root account in AWS. For this reason you are going to create a new account which you'll use regularly as the admin account.
  
- Create a user with password credentials
- Add the newly created user to a group called "admin" and attach to it the policy called "Administrator Access"
- Make sure the user has a tag called with the key Role and the value DevOps
  
</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to the AWS IAM service

2. Click on "Users" in the right side menu (right under "Access Management")

3. Click on the button "Add users"

4. Insert the user name (e.g. mario)

5. Select the credential type: "Password"

6. Set console password to custom and click on "Next"

7. Click on "Add user to group"

8. Insert "admin" as group name

9. Check the "AdministratorAccess" policy and click on "Create group"

10. Click on "Next: Tags"

11. Add a tag with the key Role and the value DevOps

12. Click on "Review" and then create on "Create user"

13. Solution using Terraform
    ```Terraform     
    resource "aws_iam_group_membership" "team" {
      name = "tf-testing-group-membership"

      users = [
        aws_iam_user.newuser.name,

      ]

      group = aws_iam_group.admin.name
    }

    resource "aws_iam_group_policy_attachment" "test-attach" {
      group      = aws_iam_group.admin.name
      policy_arn = "arn:aws:iam::aws:policy/AdministratorAccess"
    }
    resource "aws_iam_group" "admin" {
      name = "admin"
    }

    resource "aws_iam_user" "newuser" {
      name = "newuser"
      path = "/system/"

      tags = {
        Role = "DevOps"
      }
    }
    ```

</details>

<details>
<summary><b><i>2.Password Policy & MFA
  
- Create password policy with the following settings:
- At least minimum 8 characters
- At least one number
- Prevent password reuse
- Then enable MFA for the account.

</i></b></summary>

$\color{green}{\text{Answer}}$

Password Policy:

1. Go to IAM service in AWS

2. Click on "Account settings" under "Access management"

3. Click on "Change password policy"

4. Check "Enforce minimum password length" and set it to 8 characters

5. Check "Require at least one number"

6. Check "Prevent password reuse"

7. Click on "Save changes"

MFA:

1. Click on the account name

2. Click on "My Security Credentials"

3. Expand "Multi-factor authentication (MFA)" and click on "Activate MFA"

4. Choose one of the devices

5. Follow the instructions to set it up and click on "Assign MFA"

6. Solution using Terraform:
   ```Terraform
   resource "aws_iam_account_password_policy" "strict" {
   minimum_password_length        = 8
   require_numbers                = true
   allow_users_to_change_password = true
   password_reuse_prevention      = 1
   }
   ```
   
   Note: You cannot add MFA through terraform, you have to do it in the GUI.

</details>

<details>
<summary><b><i>3.Create a Role
  
Create a basic role to provide EC2 service with Full IAM access permissions. In the end, run from the CLI (or CloudShell) the command to verify the role was created.</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to AWS console -> IAM

2. Click in the left side menu on "Access Manamgement" -> Roles

3. Click on "Create role"

4. Choose "AWS service" as the type of trusted entity and then choose "EC2" as a use case. Click on "Next"

5. In permissions page, check "IAMFullAccess" and click on "Next" until you get to "Review" page

6. In the "Review" page, give the role a name (e.g. IAMFullAcessEC2), provide a short description and click on "Create role"

7. `aws iam list-roles` will list all the roles in the account, including the one we've just created.

</details>

<details>
<summary><b><i>4.Credential Report
  
- Create/Download a credential report
- Answer the following questions based on the report:
- Are there users with MFA not activated?
- Are there users with password enabled that didn't
- Explain the use case for using the credential report

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to the AWS IAM service

2. Under "Access Reports" click on "Credential report"

3. Click on "Download Report" and open it once it's downloaded

4. Answer the questions in this exercises by inspecting the report

Note: The credential report is useful to identify whether there any users who need assistance or attention in regards to their security. For example a user who didn't change his password for a long time and didn't activate MFA.

</details>

<details>
<summary><b><i>5.Access Advisor

Go to the Access Advisor and answer the following questions regarding one of the users:

- Are there services this user never accessed?
- What was the last service the user has accessed?
- What the Access Advisor is used/good for?

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to AWS IAM service and click on "Users" under "Access Management"

2. Click on one of the users

3. Click on the "Access Advisor" tab

4. Check which service was last accessed and which was never accessed

Note: Access Advisor can be good to evaluate whether there are services the user is not accessing (as in never or not frequently). This can be help in deciding whether some permissions should be revoked or modified.

</details>

### EC2

<details>
<summary><b><i>6.Launch EC2 Web Instance

Launch one EC2 instance with the following requirements:

- Amazon Linux 2 image
- Instance type: pick up one that has 1 vCPUs and 1 GiB memory
- Instance storage should be deleted upon the termination of the instance
- When the instance starts, it should install:
- Install the httpd package
- Start the httpd service
- Make sure the content of `/var/www/html/index.html` is `I made it! This is is awesome!`
- It should have the tag: "Type: web" and the name of the instance should be "web-1"
- HTTP traffic (port 80) should be accepted from anywhere

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Choose a region close to you

2. Go to EC2 service

3. Click on "Instances" in the menu and click on "Launch instances"

4. Choose image: Amazon Linux 2

5. Choose instance type: t2.micro

6. Make sure "Delete on Termination" is checked in the storage section

7. Under the "User data" field the following:
   ```Linux
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>I made it! This is is awesome!</h1>" > /var/www/html/index.html
   ```

8. Add tags with the following keys and values:
   - key `"Type"` and the value `"web"`
   - key `"Name"` and the value `"web-1"`
  
9. In the security group section, add a rule to accept HTTP traffic (TCP) on port 80 from anywhere

10. Click on "Review" and then click on "Launch" after reviewing.

11. If you don't have a key pair, create one and download it.

12. Solution using Terraform:
    ```Terraform
    provider "aws" {
      region = "us-east-1" // Or your desired region
    }

    resource "aws_instance" "web_server" {
      ami           = "ami-12345678" // Replace with the correct AMI for Amazon Linux 2
      instance_type = "t2.micro" // Or any instance type with 1 vCPU and 1 GiB memory

      tags = {
        Name = "web-1"
        Type = "web"
      }

      root_block_device {
        volume_size           = 8 // Or any desired size
        delete_on_termination = true
      }

      provisioner "remote-exec" {
        inline = [
          "sudo yum update -y",
          "sudo yum install -y httpd",
          "sudo systemctl start httpd",
          "sudo bash -c 'echo \"I made it! This is awesome!\" > /var/www/html/index.html'",
          "sudo systemctl enable httpd"
        ]

        connection {
          type        = "ssh"
          user        = "ec2-user"
          private_key = file("~/.ssh/your_private_key.pem") // Replace with the path to your private key
          host        = self.public_ip
        }
      }

      security_group_ids = [aws_security_group.web_sg.id]
      }

    resource "aws_security_group" "web_sg" {
      name        = "web_sg"
      description = "Security group for web server"
  
      ingress {
        from_port   = 80
        to_port     = 80
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }
    }
    ```

</details>

<details>
<summary><b><i>7.Security Groups

For this exercise you'll need:
1. EC2 instance with web application
2. Security group inbound rules that allow HTTP traffic

- List the security groups you have in your account, in the region you are using
- Remove the HTTP inbound traffic rule
- Can you still access the application? What do you see/get?
- Add back the rule
- Can you access the application now?

</i></b></summary>

$\color{green}{\text{Answer}}$

Console:

1. Go to EC2 service - > Click on "Security Groups" under "Network & Security" You should see at least one security group. One of them is called "default"\

2. Click on the security group with HTTP rules and click on "Edit inbound rules". Remove the HTTP related rules and click on "Save rules"

3. No. There is a time out because we removed the rule allowing HTTP traffic.

4. Click on the security group -> edit inbound rules and add the following rule:
   - Type: HTTP
   - Port range: 80
   - Source: Anywhere -> 0.0.0.0/0

5. Yes

CLI:

1. `aws ec2 describe-security-groups` -> by default, there is one security group called "default", in a new account

2. Remove the rule:
   ```
   aws ec2 revoke-security-group-ingress \
    --group-name someHTTPSecurityGroup
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
   ```

3. No. There is a time out because we removed the rule allowing HTTP traffic.

4. Add the rule we remove:
   ```
   aws ec2 authorize-security-group-ingress \
    --group-name someHTTPSecurityGroup
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
   ```

5. Yes

</details>

<details>
<summary><b><i>8.IAM Roles

1. Running EC2 instance without any IAM roles (so you if you connect the instance and try to run AWS commands, it fails)
2. IAM role with "IAMReadOnlyAccess" policy

- Attach a role (and if such role doesn't exists, create it) with "IAMReadOnlyAccess" policy to the EC2 instance
- Verify you can run AWS commands in the instance

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to EC2 service

2. Click on the instance to which you would like to attach the IAM role

3. Click on "Actions" -> "Security" -> "Modify IAM Role"

4. Choose the IAM role with "IAMReadOnlyAccess" policy and click on "Save"

5. Running AWS commands now in the instance should work fine (e.g. `aws iam list-users`)

</details>

<details>
<summary><b><i>9.Spot Instances

Create two Spot instances using a Spot Request with the following properties:
   - Amazon Linux 2 AMI
   - 2 instances as target capacity (at any given point of time) while each one has 2 vCPUs and 3 GiB RAM

Create a single Spot instance using Amazon Linux 2 and t2.micro

</i></b></summary>

$\color{green}{\text{Answer}}$

Create Spot Fleets:

1. Go to EC2 service

2. Click on "Spot Requests"

3. Click on "Request Spot Instances" button

4. Set the following values for parameters:
   - Amazon Linux 2 AMI
   - Total target capacity -> 2
   - Check "Maintain target capacity"
   - vCPUs: 2
   - Memory: 3 GiB RAM

5. Click on Launch

Create a single Spot instance:

1. Go to EC2 service

2. Click on "Instances"

3. Click on "Launch Instances"

4. Choose "Amazon Linux 2 AMI" and click on "Next"

5. Choose t2.micro and click on "Next: Configure Instance Details"

6. Select "Request Spot instances"

7. Set Maximum price above current price

8. Click on "Review and Launch"

</details>

<details>
<summary><b><i>10.Elastic IP

An EC2 instance with public IP (not elastic IP)

- Write down the public IP of your EC2 instance somewhere and stop & start the instance. Does the public IP address is the same? why?
- Handle this situation so you have the same public IP even after stopping and starting the instance

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to EC2 service -> Instances

2. Write down current public IP address

3. Click on "Instance state" -> Stop instance -> Stop

4. Click on "Instance state" -> Start Instance

5. Yes, the public IP address has changed

6. Let's use an Elastic IP address

7. In EC2 service, under "Network & Security" click on "Elastic IP"

8. Click on the "Allocate elastic IP address" button

9. Make sure you select "Amazon's pool of IPv4 addresses" and click on "Allocate"

10. Click on "Actions" and then "Associate Elastic IP address" 1. Select "instance", choose your instance and provide its private IP address 2. Click on "Associate"

11. Now, if we go back to the instance page, we can see it is using the Elastic IP address as its public IP

Note: to remove it, use "disassociate" option and don't forget to also release it so you won't be billed.

</details>

<details>
<summary><b><i>11.Placement Groups

- Create a placement group. It should be one with a low latency network. Make sure to launch an instance as part of this placement group.
- Create another placement group. This time high availability is a priority

</i></b></summary>

$\color{green}{\text{Answer}}$

Create a placement group:

1. Go to EC2 service

2. Click on "Placement Groups" under "Network & Security"

3. Click on "Create placement group"

4. Give it a name and choose the "Cluster" placement strategy because the requirement is low latency network

5. Click on "Create group"

6. Go to "Instances" and click on "Launch an instance". Choose any properties you would like, just make sure to check "Add instance to placement group" and choose the placement group you've created

Create another placement group:

1. Go to EC2 service

2. Click on "Placement Groups" under "Network & Security"

3. Click on "Create placement group"

4. Give it a name and choose the "Spread" placement strategy because the requirement is high availability as top priority

5. Click on "Create group"

</details>

<details>
<summary><b><i>12.Elastic Network Interfaces

An EC2 instance with network interface

- Create a network interface and attach it to the EC2 instance that already has one network interface
- Explain why would anyone use two network interfaces

</i></b></summary>

$\color{green}{\text{Answer}}$

Create a network inferface:

1. Go to EC2 service

2. Click on "Network Interfaces" under "Network & Security"

3. Click on "Create network interface"

4. Provide a description

5. Choose a subnet (one that is in the AZ as the instance)

6. Optionally attach a security group and click on "Create network interface"

7. Click on "Actions" -> "Attach" and choose the instance to attach it to

8. If you go now to "Instances" page you'll see your instance has two network interfaces

Why two interfaces:

You can move the second network interface between instances. This allows us to create kind of a failover mechanism between the instances.

</details>

<details>
<summary><b><i>13.Hibernate an Instance

- Create an instance that supports hibernation
- Hibernate the instance
- Start the instance
- What way is there to prove that instance was hibernated from OS perspective?

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Create an instance that supports hibernation

2. Go to EC2 service

3. Go to instances and create an instance

4. In "Configure instance" make sure to check "Enable hibernation as an additional stop behavior"

5. In "Add storage", make sure to encrypt EBS and make sure the size > instance RAM size (because hibernation saves the RAM state)

6. Review and Launch

7. Hibernate the instance

8. Go to the instance page

9. Click on "Instance state" -> "Hibernate instance" -> Hibernate

10. Instance state -> Start

11. Run the "uptime" command, which will display the amount of time the system was up

</details>

<details>
<summary><b><i>14.EBS Volume Creation

One EC2 instance that you can get rid of

- Create a volume in the same AZ as your instance, with the following properties:
  - gp2 volume type
  - 4 GiB size
- Once created, attach it to your EC2 instance
- Remove your EC2 instance. What happened to the EBS volumes attached to the EC2 instance?

</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to EC2 service

2. Click on "Volumes" under "Elastic Block Store"

3. Click on "Create Volume"
4. Select the following properties

5. gp2 volume type

6. 4 GiB size

7. The same AZ as your instance

8. Click on "Create volume"

9. Right click on the volume you've just created -> attach volume -> choose your EC2 instance and click on "Attach"

10. Terminate your instance

11. The default EBS volume (created when you launched the instance for the first time) will be deleted (unless you didn't check "Delete on termination"), but the volume you've created as part of this exercise, will remain

Note: don't forget to remove the EBS volume you've created in this exercise

</details>

<details>
<summary><b><i>15.EBS Snapshots

EBS Volume

- Create a snapshot of an EBS volume
- Verify the snapshot was created
- Move the data to another region
- Create a volume out of it in a different AZ

</i></b></summary>

$\color{green}{\text{Answer}}$

Create a snapshot:

1. Go to EC2 service

2. Click on "Volumes" under "Elastic Block Store"

3. Right click on the chosen volume -> Create snapshot

4. Insert a description and click on "Create Snapshot"

Verify the snapshot:

1. Click on "Snapshots" under "Elastic Block Store"

2. You should see the snapshot you've created

Move the data:

1. Select the snapshot and click on Actions -> Copy

2. Select a region to where the snapshot will be copied

Create a volume:

1. Select the snapshot and click on Actions -> Create volume

2. Choose a different AZ

3. Click on "Create Volume"

</details>

### S3

<details>
<summary><b><i>Create buckets

Create the following buckets:

Private bucket
  - eu-west-2 region
  - Upload a single file to the bucket. Any file.

Public bucket
  - eu-west-1 region
  - Versioning should be enabled

</i></b></summary>

$\color{green}{\text{Answer}}$

For the first bucket:

1. Go to S3 service in the AWS console. If not in buckets page, click on "buckets" in the left side menu

2. Click on "Create bucket"

3. Give a globally unique name for your bucket

4. Choose the region "eu-west-2"

5. Click on "Create bucket"

6. Click on the bucket name

7. Under "objects" click on "Upload" -> "Add files" -> Choose file to upload -> Click on "Upload"

8. For the second bucket:

Go to S3 service in the AWS console. If not in buckets page, click on "buckets" in the left side menu

1. Click on "Create bucket"

2. Give a globally unique name for your bucket

3. Choose the region "eu-west-1"

4. Make sure to uncheck the box for "Private bucket" to make it public

5. Make sure to check the enable box for "Bucket Versioning"

6. Click on "Create bucket"

Solution using Terraform:
```Terraform
resource "aws_s3_bucket" "private_bucket" {
  bucket = "my-first-private-bucket"
  region = "eu-west-2"
  acl = "private" 

  tags = {
    Name        = "My First Private Bucket"
    Environment = "Exercise"
  }
}

resource "aws_s3_bucket_acl" "private_bucket_acl" {
  bucket = aws_s3_bucket.private_bucket.id
  acl    = "private"
}

resource "aws_s3_bucket" "public_bucket" {
  bucket = "my-first-public-bucket"
  region = "eu-west-1"

  tags = {
    Name        = "My First Public Bucket"
    Environment = "Exercise"
  }

  versioning {
    enabled = true
  }
}

resource "aws_s3_bucket_acl" "public_bucket_acl" {
  bucket = aws_s3_bucket.public_bucket.id
  acl    = "public-read"
}

resource "aws_s3_bucket_object" "bucket_object" {
  bucket   = "my-first-private-bucket"
  key      = "some_object_key"
  content  = "object content"
}
```

</details>
