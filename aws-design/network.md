This document describes the design of our cloud network. The key point is this is designed to be run as a blackbox with near identical internals in each environment. The cloudformation set of repos describes and enforces this design.

# VPC Design
Our network is made up of two vpcs, infrastructure vpc to house shared resources like the consul cluster, and services like ntp and application vpc to house custom business services.

These two vpcs are then linked together and have routes created to each other to allow for communication. 

Each vpc gets an internet gateway attachments for the public subnet routes.

# Subnet Design
Each vpc shares the same subnet structure. This includes a private and public subnet in two availabilty zones (AZ) per region. Currently we do not use the third AZ, but we could expand this easily in the future.

* VPC: 10.x.0.0/16
  * AZ 1
    * private: 10.x.0.0/19
    * public: 10.x.32.0/20
    * reserved: 10.x.48.0/20
  * AZ 2
    * private: 10.x.64.0/19
    * public: 10.x.96.0/20
    * reserved: 10.x.112.0/20
  * AZ 3 - unused

This gives us 8k ips in each private subnet, and 4k each public. We want more private ips since most instances will reside in the private subnets with load balancers exposing their services in the public.

# Route Design
We need to create a few different routes to allow communication between our subnets and to the internet. 

Each private subnet gets a NAT instance, described by the nat-curbix repo, and a route to allow the private subnet outbound internet access. The current NAT monitoring script that exists in aws-startup-utils repo is currently NOT scaleable to 3 AZs, but it should be with minimal effort.

Each vpc then receives routes to its paired vpc. This allows for apps to communicate with infrastructure services.

Finally each vpc has a route to the vpcs internet gateway for the public subnets to allow for direct communication.

PLACEHOLDER - describe how we route to VPN

# Security
## Bastion Hosts
Each vpc has two bastion hosts to allow for ssh access into the vpcs. Since security groups are per vpc, this means that gaining ssh access to one vpc does not automatically carry over to the other vpc.

The bastion host resides in the private subnet, exposed to the vpn, and receives a ```external security group``` that is referenced by the ```internal security group``` that is applied to all instances in the vpc.

## NATs
The NAT instances control outbound internet access for the all the instances in our private subnets. 

Only the internet facing load balancers and the NAT instances themselves should be routeable to the public internet.

The NAT instances have a security group controlling which ports can be accessed. Typically ntp, http, https, syslog, icmp, and possibly other ports will be open to all internal applications via the vpcs cidr block.
