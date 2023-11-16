---
layout     : post
author     : Alan Tai
date       : 2023-11-15
title      : Create your own personal VPN with Tailscale on AWS using Terraform
image      : posts/2023/11/15/annie-spratt-YopBL6zM7F8-unsplash.jpg
categories : Automation
---
## Introduction

In today's digital age, where our online activities are constantly being tracked and monitored, it is more important than ever to take steps to protect our privacy and security. One of the most effective ways to do this is to use a personal VPN. A VPN, or virtual private network, is an invaluable tool for protecting your online activities by encrypting your internet traffic and routing it through a secure server, making it virtually impossible for anyone to track or intercept your data.

This technical article will guide you through the process of creating a personal VPN using Tailscale on Amazon Web Services (AWS) using Terraform. Throughout this tutorial, you will learn how to:

1. Create an AWS EC2 instance to serve as your Tailscale VPN endpoint.
2. Install and configure the Tailscale daemon on the AWS EC2 instance.
3. Provision a Tailscale mesh network and join your AWS EC2 instance to the mesh.
4. Connect your devices to the Tailscale mesh network to enjoy secure and private internet access.

By following this comprehensive tutorial, you will gain hands-on experience in setting up your own personal VPN with Tailscale on AWS using Terraform, empowering you to safeguard your online activities and enhance your overall cybersecurity posture.

### Why Terraform?

Provisioning and managing cloud infrastructure can be a complex and time-consuming task, especially when dealing with multiple resources and configurations. This is where Terraform, an infrastructure as code (IaC) tool, comes into play. Terraform simplifies the process of infrastructure management by allowing you to define and provision your cloud resources in a declarative and repeatable manner. By using Terraform, you can easily create and manage your Tailscale VPN infrastructure with minimal configuration effort.

### Why Tailscale?

Tailscale distinguishes itself with its intuitive user interface and straightforward zero-configuration process. Unlike OpenVPN, which requires complex configuration files and manual setup, Tailscale offers a user-friendly dashboard and simple installation procedures. This makes it easier for non-technical users to set up and manage their VPNs.

Tailscale utilises the WireGuard protocol, a modern VPN protocol known for its superior performance, reduced overhead, and robust security features. Compared to OpenVPN's older protocols, WireGuard offers faster connection speeds, lower latency, and improved encryption algorithms.

### Why AWS?

AWS offers a wide range of pricing options that are cost-effective and flexible, allowing you to tailor your VPN infrastructure to your specific needs and budget. It has a well-established ecosystem of third-party tools, documentation, and tutorials specific to Terraform and VPN deployment. This extensive support network makes it easier to find resources and troubleshoot any issues that might arise.

## Setup

1. Install Terraform
2. A working AWS account
3. An IAM User Access Key ID and Secret Key pair
4. A Tailscale account and API key

### Install Terraform

Terraform can be installed on a variety of operating systems, including Linux, macOS, and Windows.

#### Installing Terraform on Linux

For Ubuntu and Debian-based distros, you can use the following command to install Terraform:
`sudo apt-get install terraform`

For Red Hat Enterprise Linux (RHEL) and CentOS-based distros, you can use the following command to install Terraform:
`sudo yum install terraform`

Alternatively, you can download the Terraform binary for your Linux distribution from the official Terraform website.

1. Unzip the downloaded file to a directory of your choice.
2. Add the Terraform directory to your system PATH environment variable.

For more complex installation scenarios or configurations, please refer to the [official Terraform documentation](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).

#### Installing Terraform on macOS

Use Homebrew to install Terraform:

1. `brew tap hashicorp/tap`
2. `brew install hashicorp/tap/terraform`

Alternatively, you can download the Terraform binary for macOS from the official Terraform website.

1. Unzip the downloaded file to a directory of your choice.
2. Add the Terraform directory to your system PATH environment variable.

#### Installing Terraform on Windows

1. Download the Terraform binary for Windows from the official Terraform website.
2. Unzip the downloaded file to a directory of your choice.
3. Add the Terraform directory to your system PATH environment variable.
4. Open a command prompt and verify that Terraform is installed by typing the command: `terraform version`

### A working AWS account

If you do have currently have a working AWS account, you may:

1. Go to the AWS home page: https://aws.amazon.com/
2. Click on the "Create an AWS Account" button.
3. Enter your personal details.
4. Verify your email address.
5. Complete the AWS Account Sign-up process by providing your billing information.

### An IAM User Access Key ID and Secret Key pair

You will need to generate an Access Key ID and Secret Key pair to be used by Terraform for EC2 instance provisioning.

1. In the AWS Management Console, go to the IAM (Identity and Access Management) service.
2. Click on the "Users" menu item.
3. Click on the "Create user" button.
4. Enter a user name for your IAM user.
5. Click on the "Attach policies directly" option.
6. Choose the "AmazonEC2FullAccess" and "AmazonSSMFullAccess" policies. "AmazonEC2FullAccess" policy is for EC2 instance provisioning. "AmazonSSMFullAccess" policy is for storing the generated SSH key pair in the AWS Systems Manager Parameter Store if you need to connect to your EC2 instance later.
7. Click on the "Next" button.
8. (Optional) Add tags to your IAM user.
9. Click on the "Create user" button.
10. Copy the Access Key ID and the Secret Key for your IAM user. Keep them safe and secure.

For detailed instructions and documentation, please refer to the official AWS documentation:

* **AWS Account:** https://aws.amazon.com/resources/create-account/
* **IAM User and Access Key:** https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html
* **API Key and Secret:** https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-setup-api-key-with-console.html

### A Tailscale account and API key

Creating a Tailscale account and obtaining an API key for creating a Tailscale VPN mesh network is a straightforward process that can be completed in a few simple steps.

1. **Go to the Tailscale website:** https://tailscale.com/
2. **Click on the "Sign up" button:** This will take you to the registration page.
3. **Enter your email address:** Provide a valid email address that you will use to log in to your Tailscale account.
4. **Create a password:** Choose a strong password that you will use to secure your Tailscale account.
5. **Click on the "Create Account" button:** This will create your Tailscale account.
6. **Verify your email address:** Check your email inbox for a verification message from Tailscale. Click on the link in the message to verify your email address.
7. **Sign in to your Tailscale account:** Use your email address and password to log in to your Tailscale account.
8. **Go to the "API Keys" page:** In the Tailscale web console, click on the "Settings" tab and then select "Keys" under Personal Settings from the sidebar menu.
9. **Click on the "Generate access token…" button:** This will generate a new API key.
10. **Copy the API key to a secure location:** You will need this credential to use the Tailscale API for Terraform to manage your VPN mesh network.
11. **Copy the name of your tailnet:** This is a unique name to identify your Tailscale mesh VPN network.

## The magic

The steps illustrated here are based on this code repository: [https://github.com/ayltai/terraform-tailscale](https://github.com/ayltai/terraform-tailscale). For the sake of simplicity, only the most important parts are highlighted in this article. Please refer to the repository for a working example.

### Create a Terraform configuration file

Create a Terraform configuration file named "`main.tf`" in your project root directory. This file will define the infrastructure components for AWS resources and the Tailscale mesh network.

### Define a Terraform backend

This is where your Terraform state resides. It can be a local file or an AWS S3 bucket. Here, I used [Terraform Cloud](https://www.terraform.io/).

```java
terraform {
  required_version = ">= 1.5.0"

  backend "remote" {
    hostname     = "app.terraform.io"
    organization = "tailscale"

    workspaces {
      name = "main"
    }
  }

  ...
```

### Define the providers

We will use the AWS and Tailscale providers. Additionally, we will need the TLS and Null provider.

```java
terraform {
  ...

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.6"
    }

    null = {
      source  = "hashicorp/null"
      version = "~> 3.2"
    }

    tailscale = {
      source  = "tailscale/tailscale"
      version = "~> 0.13"
    }

    tls = {
      source  = "hashicorp/tls"
      version = "~> 4.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

provider "tailscale" {
  api_key = var.tailscale_api_key
  tailnet = var.tailscale_tailnet
}
```

### Define an AWS VPC

```java
resource "aws_vpc" "this" {
  cidr_block = var.vpc_cidr_block
}

resource "aws_subnet" "this" {
  vpc_id            = aws_vpc.this.id
  cidr_block        = var.subnet_cidr_block
  availability_zone = "${var.aws_region}a"
}
```

### Define a public IPv4 address for public access

```java
resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id
}

resource "aws_eip" "this" {
  instance = aws_instance.this.id
  domain   = "vpc"

  depends_on = [
    aws_internet_gateway.this,
  ]

  lifecycle {
    create_before_destroy = true
  }
}

resource "aws_route_table" "this" {
  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.this.id
  }
}

resource "aws_route_table_association" "this" {
  route_table_id = aws_route_table.this.id
  subnet_id      = aws_subnet.this.id
}
```

### Define the ingress and egress ports for public access

```java
resource "aws_security_group" "this" {
  name   = "${var.server_hostname}-${var.aws_region}"
  vpc_id = aws_vpc.this.id

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"

    cidr_blocks = [
      "0.0.0.0/0",
    ]
  }

  egress {
    from_port = 0
    to_port   = 0
    protocol  = "-1"

    cidr_blocks = [
      "0.0.0.0/0",
    ]
  }
}
```

### Generate an RSA key pair for accessing the EC2 instance via SSH

This will generate a key pair of length 4096 bytes using the RSA algorithm. The key pair will be stored in the AWS Systems Manager Parameter Store if you need to access your EC2 instance later.

```java
resource "tls_private_key" "this" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "this" {
  key_name   = "${var.server_hostname}-${var.aws_region}"
  public_key = tls_private_key.this.public_key_openssh
}

resource "aws_ssm_parameter" "this" {
  name  = "/tailscale/${var.server_hostname}-${var.aws_region}"
  type  = "SecureString"
  value = tls_private_key.this.private_key_pem
}
```

### Provision of an EC2 instance

```java
resource "aws_instance" "this" {
  ami           = data.aws_ami.this.id
  instance_type = var.server_instance_type
  subnet_id     = aws_subnet.this.id
  key_name      = aws_key_pair.this.key_name

  vpc_security_group_ids = [
    aws_security_group.this.id,
  ]

  root_block_device {
    volume_size = var.server_storage_size
  }
}
```

You can customise the Terraform configuration to suit your specific requirements, such as using a different EC2 instance type or AMI.

### Install a Tailscale VPN daemon

Create a `remote-exec` provisioner block within the null resource block. This provisioner will execute commands on the EC2 instance after it is provisioned. This provisioner will be used to download and install the Tailscale VPN daemon on the instance.

Here is a detailed explanation of what it does:

1. Install the packages required for running the subsequent commands:

* `sudo apt update`
* `sudo apt -qq install software-properties-common apt-transport-https ca-certificates lsb-release curl -y`

2. Add Tailscale package repository to the system's package repository list:

* `curl -fsSL ${var.tailscale_package_url}.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null`
* `curl -fsSL ${var.tailscale_package_url}.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list`

3. Download and install Tailscale:

* `sudo apt update`
* `sudo apt -qq install tailscale -y`

4. Update the firewall rules to allow the VPN to do its work:

* `sudo sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf`
* `sudo sed -i 's/#net.ipv6.conf.all.forwarding=1/net.ipv6.conf.all.forwarding=1/' /etc/sysctl.conf`
* `sudo sysctl -p`

5. Start the Tailscale daemon and request to act as an exit node:

* `sudo tailscale up — advertise-exit-node — hostname=${var.server_hostname}-${var.aws_region} — authkey=${tailscale_tailnet_key.this.key}`

6. Automatically start the Tailscale daemon on system startup:

* `sudo systemctl enable — now tailscaled`

```java
resource "null_resource" "this" {
  triggers = {
    always_run = timestamp()
  }

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt -qq install software-properties-common apt-transport-https ca-certificates lsb-release curl -y",
      "curl -fsSL ${var.tailscale_package_url}.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null",
      "curl -fsSL ${var.tailscale_package_url}.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list",
      "sudo apt update",
      "sudo apt -qq install tailscale -y",
      "sudo sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf",
      "sudo sed -i 's/#net.ipv6.conf.all.forwarding=1/net.ipv6.conf.all.forwarding=1/' /etc/sysctl.conf",
      "sudo sysctl -p",
      "sudo tailscale up --advertise-exit-node --hostname=${var.server_hostname}-${var.aws_region} --authkey=${tailscale_tailnet_key.this.key}",
      "sudo systemctl enable --now tailscaled",
    ]

    connection {
      agent       = false
      timeout     = var.timeout
      host        = aws_eip.this.public_ip
      private_key = tls_private_key.this.private_key_pem
      user        = var.server_username
    }
  }
}
```

### Define the variable values for your deployment

Create a Terraform configuration file named “variables.tf” like this one. Change the values to suit your purpose. These are the most common variables that you may want to change:

* `aws_region`: The location of your VPN. You can find the value in the "Region Code" column on this website.
* `tailscale_api_ke`y: Your Tailscale API key.
* `tailscale_tailnet`: Your Tailscale tailnet name.

Additionally, Terraform will need your AWS Access Key ID and Secret Key to manage the resources on your behalf. You can provide them by setting the Access Key ID to `AWS_ACCESS_KEY_ID` and the Secret Key to `AWS_SECRET_ACCESS_KEY` environment variables.

### Apply the Terraform configuration

Run the following commands to apply the Terraform configuration:

1. `terraform init`
2. `terraform plan`
3. `terraform apply`

This will provision the EC2 instance, install the Tailscale VPN daemon, and configure it to act as the exit node.

Once the Terraform apply process completes, verify that the EC2 instance has successfully requested to join the Tailscale mesh network. You can use the Tailscale web console to check the status of the mesh network and subsequently approve this newly provisioned EC2 instance to act as an exit node.

## What's next

Congratulations on successfully creating your own personal VPN with Tailscale on AWS using Terraform. You have now set up a secure and private network that you can use to access the internet from any device, anywhere in the world.

In this tutorial, you learnt how to:

1. Use Terraform to create an AWS EC2 instance to serve as your Tailscale VPN endpoint.
2. Use Terraform to install and configure the Tailscale daemon on your AWS EC2 instance.
3. Use Terraform to provision a Tailscale mesh network and join your AWS EC2 instance to the mesh.
4. Connect your personal devices to the Tailscale mesh network to enjoy secure and private internet access.

You can now enjoy the benefits of a personal VPN, such as:

1. Protecting your privacy by encrypting your internet traffic.
2. Securing your public Wi-Fi use by routing your traffic through a secure server.
3. Accessing geo-blocked content by routing your traffic through a server in another country.
4. Safeguarding your online banking by encrypting your financial information.

I hope this tutorial has been helpful. If you have any questions, please feel free to leave a comment.