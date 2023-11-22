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
4. Click Create subnet.
The new subnet will be deployed into the selected VPC, and into the selected Availability Zone.

Next, you will need to set up the route table.

## Public Route table:

5. In the left-hand navigation pane, click the Filter by VPC field and select the VPC-project VPC from the drop-down.
6. In the left navigation pane, click Route Tables.
7. Click Create route table.
8. Configure the following route table settings:
* Name: Enter *PublicRouteTable*
* VPC: Select the VPC-project VPC from the drop-down menu
9. Scroll to the bottom of the page and click Create route table.
10. On the route details page, switch to the Routes tab and click Edit routes.
11. Click Add route.
12. Configure the following route settings:
* Destination: Enter 0.0.0.0/0
* Target: Select Internet Gateway, then demo-gw13. 
13. Click Save changes.
Up next, you will change the default route table of the public subnet to include the new route table.

## Route table association:
14. In the left-hand navigation pane, click Subnets.

15. Select the Public-A subnet and click the Route table tab

16. Click the Edit route table association button.
17.  Select PublicRouteTable from the Route table ID drop-down menu and confirm the gw(demo-gw) we have created.
18. Click Save.
In this step, we created a public subnet in your VPC and associated it with a route table with access to the public internet.

## Step 5: Creating a Bastion Host
1. In the AWS Management Console search bar, enter EC2, and click the EC2 result under Services.
2. To see available instances, click Instances in the left-hand menu.
3. Click Launch instances.
4. In the Name and tags section, enter bastion under Name.

5. In the Application and OS Images section, select the Amazon Linux option under Quick Start.
6. In the Instance Type section, you should not change any options. Simply make sure the default t2.micro is selected.
7. In the Key pair section, select the keypair.
8.  In the Network settings section, click Edit, and configure the following instance details:
* VPC: Select the VPC-project VPC
* Subnet: Select the Public-A | us-west-2a subnet
* Auto-assign Public IP: Select Enable 
* Firewall: Select Create security group
* Security group name: Enter SG-bastion
* Description: Enter SG for bastion host. SSH access only
* Type: SSH
* Protocol: TCP
* Port: 22
* Source type: Anywhere
* Source: 0.0.0.0/0

_Note: it isn't a best practice to set the source to any IP,you could set the source field to My IP in the drop-down menu to only allow your IP for improved security_.

9. Review the Summary section and click Launch instance.

In this step, we launched an EC2 instance with a public IP address in your public subnet that will be used as a bastion host.






 





































































