# Terraform-Vpc-Ec2
Terraform-Vpc-Ec2
Assignment: Provisioning an AWS VPC and EC2 Instance with Terraform
🔧 Prerequisites
AWS CLI configured (aws configure)

IAM user with permissions to create VPC, EC2, etc.

Terraform installed (terraform -v)

A key pair in AWS (to SSH into EC2, optional but useful)
create iam user asign full admin access to it 
Step 1 - Lauch ec3 instance conncet ssh
🔧 Step 1: Reconfigure Your AWS Credentials
Run this in your terminal:

bash
Copy
Edit
aws configure
You’ll be prompted to enter:

AWS Access Key ID

AWS Secret Access Key

Default region (e.g., us-east-1)

Output format (optional, e.g., json)

Make sure:

There are no extra spaces or quotes when pasting keys.

You're using a valid IAM user with permission to call STS (GetCallerIdentity), EC2, VPC, etc.
 Run this to verify:

bash
Copy
Edit
aws sts get-caller-identity
You should see output like:

json
Copy
Edit
{
    "UserId": "ABCDEF...",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/your-user"
}
If this fails, the issue is definitely with your credentials or permissions.

Step 2- 
// install terraform 
  
 ref: https://developer.hashicorp.com/terraform/install?product_intent=terraform 
  
 sudo yum install -y yum-utils shadow-utils 
 sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo 
 sudo yum -y install terraform 

 terraform { 
   required_providers { 
     aws = { 
       source = "hashicorp/aws" 
       version = "5.78.0" 
     } 
   } 
 } 

Here is a **simple and clean step-by-step guide** to complete your assignment: **Provisioning a VPC and t2.micro EC2 instance using Terraform**.

---

## ✅ Assignment: Provisioning a VPC and EC2 (t2.micro) using Terraform

---

### 🔧 **Prerequisites**

* AWS CLI configured (`aws configure`)
* IAM user with permissions to create VPC, EC2, etc.
* Terraform installed (`terraform -v`)
* A key pair in AWS (to SSH into EC2, optional but useful)

---

### 🗂️ **Step 1: Create a Working Directory**

```bash
mkdir terraform-vpc-ec2
cd terraform-vpc-ec2
```

---

### 📄 **Step 2: Create Terraform Files**

#### 1. `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

# VPC
resource "aws_vpc" "main_vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "main_vpc"
  }
}

# Subnet
resource "aws_subnet" "main_subnet" {
  vpc_id            = aws_vpc.main_vpc.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
  tags = {
    Name = "main_subnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main_igw" {
  vpc_id = aws_vpc.main_vpc.id
}

# Route Table
resource "aws_route_table" "main_rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main_igw.id
  }
}

resource "aws_route_table_association" "rt_assoc" {
  subnet_id      = aws_subnet.main_subnet.id
  route_table_id = aws_route_table.main_rt.id
}

# Security Group
resource "aws_security_group" "web_sg" {
  name        = "web_sg"
  description = "Allow SSH"
  vpc_id      = aws_vpc.main_vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# EC2 Instance
resource "aws_instance" "web" {
  ami                    = "ami-0fc5d935ebf8bc3bc" # ✅ Valid Amazon Linux 2 AMI in us-east-1
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.main_subnet.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  associate_public_ip_address = true

  tags = {
    Name = "MyTerraformEC2"
  }
}
```

---

#### 2. `outputs.tf` (optional)

```hcl
output "instance_public_ip" {
  value = aws_instance.web.public_ip
}
```

---

### ⚙️ **Step 3: Initialize Terraform**

```bash
terraform init
```

---

### 🔍 **Step 4: Review Plan**

```bash
terraform plan
```

---

### 🚀 **Step 5: Apply and Provision**

```bash
terraform apply
```

Type `yes` when prompted.

---

### 🔍 **Step 6: Verify**

* Visit **EC2 dashboard** in AWS Console
* Confirm the **EC2 instance is running**
* You should also see:

  * The **VPC**
  * **Subnet**
  * **Internet Gateway**
  * **Security Group**

---

### 🧹 **Step 7: Destroy Resources (optional)**

```bash
terraform destroy
```

---
