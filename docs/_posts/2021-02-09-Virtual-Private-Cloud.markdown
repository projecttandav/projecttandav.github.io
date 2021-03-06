---
layout: post
title:  "Virtual Private Cloud"
date:   2021-02-09 20:27:53 -0800
categories: jekyll update
---


## Networking - VPC

### Table of Contents

- [Section Introduction](#section-introduction)
- [CIDR - Private vs Public IP](#cidr---private-vs-public-ip)
- [Default VPC Overview](#default-vpc-overview)
- [VPC Overview](#vpc-overview)
- [Subnets](#subnets)
- [Internet Gateways & Route Tables](#internet-gateways-and-route-tables)



### Section Introduction

We need to know in and out how to create, operate and manage the VPC for network guidelines.

We will focus on Amazon VPC and AWS Direcr Connect in this section.

### CIDR - Private vs Public IP

- CIDR - Classless Inter-Domain Routing <br/>
- CIDR are sued for Security Groups rules, or AWS networking in general<br/>
- They help to define an IP Range.
    - We've seen WW.XX.YY.ZZ/32 == One IP <br/>
    - We've seen 0.0.0.0/0 == all IPs <br/>
    - But we can define e.g., 192.168.0.0/26: 192.168.0.0 - 192.168.0.63 (64 IP)

**Understanding CIDR**

- A CIDR has two components
    - the base IP (XX.XX.XX.XX)
    - The Subnet Mask (/26)
- The base IP represents an IP contained in the range
- The subnet mask defines how many bits can change in the IP

- The subnet mask can two two forms 
    - 255.255.255.0 <- `less common`
    - /24 <- `more common`
<br/><br/>
- Subnet masks basically allow part of the underlying IP to get additional next values from the base IP.
    - /32 allows for 1 IP = 2^0
    - /24 allows for 256 IPs = 2^8
    - /16 allows for 65,536 IPs = 2^16
    - /0 allows for all IPs = 2^32
<br/><br/>
- Quick memo
    - /32 - no IP number can change
    - /24 - last IP number can change
    - /16 - last IP two numbers can change
    - /8 - last IP three numbers can chnage 
    - /0 - all IP numbers can change
<br/><br/>
- Little Exercise
    - 192.168.0.0/24 = ...?
        - 192.168.0.0 - 192.168.0.255 (256 IPs)
    - 192.168.0.0/16 = ...?
        - 192.168.0.0 - 192.168.0.255 (65,536 IPs)
    - 194.56.78.123/32 = ...?
        -  Just 194.56.78.123
    - 0.0.0.0/0 = ...?
        - All IPs

    - When in doubt - use this webiste: [https://www.ipaddressguide.com/cidr](https://www.ipaddressguide.com/cidr)

- Private vs Public IP (IPv4) - Allowed ranges
    - The Internet Assigned Numbers Authority (IANA) established certain blocks of IPv4 addresses for the use of private (LAN) and public (Internet) addresses
    - Private IP can only allow certain values
        - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) <= in big networks
        - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12) **<= default AWS one**
        - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) <= example: home networks
    - All the rest of the IP on the internet are public IP

### Default VPC Overview

- All new accounts have a default VPC
- New instances are launched into default VPC if no subnet is specified
- Default VPC have internet connectivity nad all instances have public IP
- We also get a public and a private DNS name
- The deafult VPC CIDR is 172.31.0.0/16. Default subnets use /20 CIDRs within the default VPC CIDR.
- Each subnet has a non-overlapping CIDR.
- We have one route table
- In Network ACL, we allow all traffic (both inbound and outbound)
- Route table have non-explicit associations with subnets and hence associated with main route table.
- Routes defined in route tables define how subnet get access to internet. One of the targets is internet gateway.

### VPC Overview

- VPC in AWS - IPv4
    - VPC Stands for Virtual Private Cloud
    - You can have multiple VPCs in a region (max 5 per region - soft limit)
    - Max CIDR per VPC is 5. Foe each CIDR
        - Min size is /28 = 16 IP addresses.
        - Max size is /16 = 65536 IP Addresses
    - Because VPC is private, only the Private IP ranges are allowed.
        - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8)
        - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12)
        - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16)
    - *Your VPC CIDR should not overlap with your other networks (ex:corporate)*

- Hands On VPC Creation
    - Click on **Create VPC** button on Your VPCs dashboard
    - Add Name tag, choose IPv4 CIDR block. (don't choose ant IPv6 CIDR block)
    - Choose Tenacy (how we are going to launch our VPC - deafult (shared hardware) or dedicated hardware)
    - Clicking on Create will create the VPC.
    - Once created, we should be able to open it and find the CIDR blocks.
    - New VPC will come with Main Route Table and Main Network ACL.
    - **Note:** We can edit CIDRs (add upto 5) upon creation too.


### Subnets

- Subnets Overview
    - Subnets are tied to a specific availability zone
    - Subnets are of two types viz., public and private.
    - Adding multiple subnets in various availability zones will have high availability.
    - Usually private subnets are smaller as we only put load balancers in there, while in private subnets we put our whole application.
- To create a Subnet
    - Click **Create Subnet** button on Subnet home page
    - Give it a name like **PublicSubnetA** as it will be in public subnet
    - Select the VPC Created above, choose AZ A, and 10.0.0.0/24 as CIDR and click create.
    - Similary for second Sybnet, use name **PublicSubnetB**, AZ B and CIDR block as 10.0.1.0/24 and click create.
    - Similary Create two private Subents with name **PrivateSubnetA** and **PrivateSubnetB** in AZ A and B respectively with CIDR blocks as 10.0.16.0/20 and 10.0.32.0/20 respectively
- Available number of IPs is always 5 less.
    - AWS reserves, 5 IP addresses (first 4 and last 1 IP Address) in each subnet
    - These 5 IPs are not available for use ans cannot eb assigned to an instance.
- Ex, if CIDR block is 10.0.0.0/24, reserved IP are:
    - 10.0.0.0: Network Address
    - 10.0.0.1: Reserved by AWS for VPC router
    - 10.0.0.2: Reserved by AWS for mapping Amazon Provided DNS
    - 10.0.0.3: Reserved by AWS for future AWS
    - 10.0.0.255: Network broadcast address. AWS does not support broadcast in a VPC, therefore the address is reserved.

### Internet Gateways And Route Tables

- Hands on
    - On Public Subnets do a right click and select Modify auto-assign setting and enable auto-assign public IPv4 addresses.
    - Spin up a Amazon Linux AMI 2 EC2 t2.micro Instance.
    - Use our newly created VPC as VPC
    - Choose PublicSubnetA as subnet
    - Add Storage, tags and security groups (port 22 from anywhere), choose key pair. review and launch
    - Newly created EC2 instance will have a private IP which is within our subnet CIDR
    - We will also have our IPV4 Public IP as well.
    - If we try to access it with ssh via public IPv4, we won't be able to, though our security groups allows TCP on Port 22.
    - This happens because it has no internet connection.
- Internet Gateways
    - Internet gateways helps our VPC instances connect with the internet
    - It scales horizontally and is highly available and redundant
    - Must be created separately from VPC
    - One VPC can only be attached to one IGW and vice versa.
    - Internet Gateway is also a NAT for the instance that have a public IPv4
    - _Internet Gateways on their own do not allow internet access ... ._
    - _Route tables must also be edited_
    
- Adding Internet Gateways
    - Go to VPC Dashboard
    - Select Internet Gateways from Left Side
    - Click *Create Internet Gateway* button
    - Give it a name tag *DemoIGW*
    - Click on Create
    - An Internet Gateway will be created
    - Go to Internet Gateways and one should be able to see newly created Internet Gateway.
    - Right Click on the internet gateway and select *Attach to VPC*. 
    - Select the VPC and click Attach (we can also see AWS CLI command to execute it through CLI)
    - *VPCs can have only one Internet Gateway attached to them.*
    - If we try to ssh the EC2 instance, we still won't be able to do so. To fix this, we need to chnage route tables.
    - So we have EC2 instance in subnet and the route table attched to VPC should point to internet gateway for a specific IP Range.
    - We will not use main route table, for anytime we create a subnet and don't assocaite it to a route table, then it will go to main route table.
    - Lets create two route tables *PublicRouteTable* and *PrivateRouteTable*  under demo VPC.
    - Lets associate Public Subnets to the Public Route Table.
    - Lets associate Private Subnets to the Private Route Table.
    - In route tables, we can see how routes work.
    - For Private route table, we can see destination as 10.0.00/16 and target as local, which is actually apt for it, as we don't want anything accessible from outside.
    - But for public subnet, we need it to be accessible from outside. So we need to add a routes in routes table
    We will add 0.0.0.0/0 as Destination and will choose internet gateway as Target, and save it. So now if we hit anuthing in VPC CIDR, it will be local and otherwise, it will be internet gateways.
    - Now if we give an attempt to access the EC2 instance in public subnet, it will work.
    - We can even try running command *sudo yum update* to esnure that it is connectec to Internet

