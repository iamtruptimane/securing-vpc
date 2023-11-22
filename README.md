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
* Subnet name: Enter *Public-A*
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
* Target: Select Internet Gateway, then demo-gw. 
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
A bastion host is typically a host that sits inside your public subnet for the purposes of SSH (and/or RDP) access.Bastion hosts are sometimes referred to as jump servers, as you jump to one, then back out of it.Once you access a bastion host (for example, by using SSH to log into it), in order to access other instances you must either set up SSH port forwarding or copy your SSH key material to the bastion host.
1. In the AWS Management Console search bar, enter EC2, and click the EC2 result under Services.
2. To see available instances, click Instances in the left-hand menu.
3. Click Launch instances.
4. In the Name and tags section, enter *bastion* under Name.

5. In the Application and OS Images section, select the Amazon Linux option under Quick Start.
6. In the Instance Type section, you should not change any options. Simply make sure the default t2.micro is selected.
7. In the Key pair section, select the keypair.
8.  In the Network settings section, click Edit, and configure the following instance details:
* VPC: Select the VPC-project VPC
* Subnet: Select the Public-A | us-west-2a subnet
* Auto-assign Public IP: Select Enable 
* Firewall: Select Create security group
* Security group name: Enter *SG-bastion*
* Description: Enter SG for bastion host. SSH access only
* Type: SSH
* Protocol: TCP
* Port: 22
* Source type: Anywhere
* Source: 0.0.0.0/0

_Note: it isn't a best practice to set the source to any IP,you could set the source field to My IP in the drop-down menu to only allow your IP for improved security_.

9. Review the Summary section and click Launch instance.

In this step, we launched an EC2 instance with a public IP address in your public subnet that will be used as a bastion host.

## Step 6: Creating a Private Subnet
In this  step, we will create a private subnet. A common use case for private subnets is to configure resources for a back-end tier, such as database servers that should not be accessible from the internet.
1. In the AWS Management Console search bar, enter VPC, and click the VPC result under Services.
2. Select VPC-project in the Filter by VPC field.
3. Click Subnets in the left navigation pane. The Subnets page lists previously created subnets.

4. Click Create Subnet and specify the following details: 
* VPC ID: Select the VPC-project VPC from the drop-down menu
* Subnet name: Enter *Private-A*
* Availability Zone: Select us-west-2a 
* CIDR block: Enter 10.0.10.0/24 as the CIDR block of your subnet
5. Click Create subnet.

The created subnet is automatically attached to the default VPC Route table and the default Network ACL. 

_If a subnet does not have a route to the Internet (0.0.0.0/0) through a gateway, the subnet is known as a private subnet_.

Next, we will create a custom private route table.

## Private route table:
6. In the navigation pane, click Route Tables, then Create route table to open the dialog box.
7. Click Create route table and configure the following:
* Name: Enter *PrivateRouteTable*
* VPC: Select VPC-project
8. Click Create route table.
9. In the PrivateRouteTable details page, in the Routes tab, click Edit routes.
10. Click Add route and configure the following route settings:
* Destination: Enter 0.0.0.0/0
* Target: Select Internet Gateway, then demo-gw

_Important:  This is a temporary target value. Later in this project, we will add a NAT device (gateway or instance) and update the Target for the PrivateRouteTable to the NAT device_.

11. Click Save changes.
12. Click Subnets from the left navigation pane, then select the Private-A subnet.

13. In the Route Table tab, and click Edit route table association.
14. Select PrivateRouteTable from the Route table ID drop-down menu.
15. Click Save.

In this step, we created a private subnet and an associated route table. The route table currently has access to the public internet through the 0.0.0.0/0 route, but as mentioned before, you will update the target to a NAT device in a later step. 

## Step 7: Creating a Network ACL for a Private Subnet
A Network Access Control List (NACL) is an optional layer of security that acts as a firewall for controlling traffic in and out of a subnet.

The VPC comes with a modifiable default network ACL and each subnet must be associated with a network ACL. If you do not explicitly associate a subnet with a network ACL, the subnet is automatically associated with the default network ACL that allows all inbound and outbound traffic.

In this setp, we will create a Network Access Control List for your private subnet.
1. In the left navigation pane, click Network ACLs under Security.
2. Click Create Network ACL.
3. Configure the following Network ACL settings.
* Name: Enter Private-NACL 
* VPC: Select VPC-project from the drop-down menu
4. Click Create network ACL

5. Select Private-NACL from the Network ACLs list and click the Subnet associations tab.
6. Click Edit subnet associations.
7. Select the check box for the Private-A subnet to associate it with the network ACL.
8. Click Save changes.

In this  step, we created a Network ACL and associated it with the private subnet in your VPC.

## Step 8: Adding Rules to a Private Network ACL
In this  step, we will create inbound and outbound rules for your private Network Access Control List (NACL).
## Inbound rules:
1. In the left navigation pane, click Network ACLs under SECURITY.
2. Select Private-NACL from the list of Network ACLs.

3. Click the Inbound rules tab below the table and click Edit inbound rules:

4. Click Add new rule and configure the following:
* Rule number: Enter 100
* Type: Select SSH
* Source: Enter 10.0.20.0/24
* Allow / Deny: Select Allow from the drop-down menu
5.  For the second rule, click Add new rule and configure the following:
* Rule number: Enter 200
* Type: Select Custom TCP Rule
* Port Range: Enter 1024-65535
* Source: Enter  0.0.0.0/0
* Allow / Deny: Select Allow from the drop-down menu
6. Click Save changes.

## Outbound rules:
7. With the Private-NACL still selected, switch to the Outbound rules tab and click Edit outbound rules.

8. Click Add new rule and configure the following:
* Rule number: Enter 100
* Type: Select HTTP from the drop-down menu
* Destination: Enter  0.0.0.0/0
* Allow / Deny: Select Allow from the drop-down menu  
9. For the second outbound rule, click Add new rule and configure the following:
* Rule number: Enter 200
* Type: Select HTTPS from the drop-down menu
* Destination: Enter  0.0.0.0/0
* Allow / Deny: Select Allow from the drop-down menu 
10. For the third outbound rule, click Add new rule and configure the following:

* Rule number: Enter 300
* Type: Select Custom TCP from the drop-down menu
* Port Range: Enter 32768-61000
* Destination: Enter  10.0.20.0/24 (The CIDR block of your public subnet)
* Allow / Deny: Select Allow from the drop-down menu 

11. Click Save changes.

In this step, we configured the inbound and outbound rules for the private Network Access Control List.






































































 





































































