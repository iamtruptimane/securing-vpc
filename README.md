# Securing your VPC using Public and Private Subnets
In this project, we will design a VPC with a public subnet, a private subnet, and a Network Address Translation (NAT) device in the public subnet. 
## Learning Objectives
Upon completion of this project we will be able to create, configure and test the following:

* Virtual Private Cloud (VPC)
* Internet Gateway
* Public and private subnets (inbound/outbound rules)
* Security groups (inbound/outbound rules for multiple purposes)
* Network access control lists (NACLs) for additional security on a private subnet
* Bastion host for SSH access from the internet to private instances
* Network Address Translation (NAT) Gateway to provide private instances access to the public internet to perform operating system updates
* Route tables associated with public and private subnets
## Prerequisites
* Elastic Cloud Compute (EC2) basics
* Conceptual understanding of Virtual Private Clouds (VPCs), subnets, network route tables, firewalls, private and public IP addresses

## Step 1: Logging In to the Amazon Web Services Console
login to your AWS account using your AWS credentials.

## Step 2: Creating a VPC
Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network you have defined.

1. In the AWS Management Console search bar, enter VPC, and click the VPC result under Services.
2. Click Your VPCs in the left navigation pane.
3. Click Create VPC to begin creating a new VPC. A Create VPC dialog box is opened for you. Specify the following VPC details:
* Resources to create: Select VPC only
* Name tag: Enter *VPC-project*
* CIDR block: Enter 10.0.0.0/16 
* Tenancy: Select Default
4. Scroll to the bottom of the page and click Create VPC.

In this step, we created the non-default VPC that will be configured with private and public subnets.

## Step 3: Creating a VPC Internet Gateway
An Internet Gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the Internet.
1. From the VPC Dashboard, click Internet Gateways in the left navigation pane.

2. Click Create internet gateway to begin creating a new gateway with the following:
* Name tag: Enter *demo-gw*
 3. Click Create Internet Gateway: 

The State of your Internet Gateway will be detached to start. Now you need to attach the new gateway to the VPC you created earlier.

4. Click Actions then Attach to VPC:

5. In the Attach to VPC form, select the VPC-project VPC from the drop-down menu.

_note: An Internet Gateway can only be attached to one VPC. Therefore, even if you have another Internet Gateway, and it's already attached to the default VPC, the drop-down menu when attaching your Internet Gateway will only include the detached VPC_.

6. Click Attach internet gateway.
7. In the Details tab, you will notice the new Internet Gateway is Attached and available to be used by EC2 instances of the attached VPC.

In this step, we created an Internet Gateway and attached it to the VPC that we created earlier. Instances in the public subnet will route traffic destined for the public internet through the internet gateway. 
## Step 4: Creating a Public Subnet
As implied by the name, a public subnet will hold resources that require ingress and/or egress to the public internet. A common use case for this is a DNS server, or a load balancer sitting in front of front-end web servers or web applications.
1. In the VPC Dashboard, click Subnets in the left navigation pane.
2. Click Create subnet.
3. Configure the following subnet details:
* VPC ID: Select  VPC-project
* Subnet name: Enter Public-A
* Availability Zone: Select us-west-2a from the drop-down menu
* CIDR block: Enter 10.0.20.0/24






























