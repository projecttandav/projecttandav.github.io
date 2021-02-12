---
layout: post
title:  "Virtual Private Cloud"
date:   2021-02-09 20:27:53 -0800
categories: jekyll update
---

Hello There

## Networking - VPC
---

### Section Introduction

We need to know in and out how to create, operate and manage the VPC for network guidelines.

We will focus on Amazon VPC and AWS Direcr Connect in this section.

### CIDR, Private vs Public IP

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
