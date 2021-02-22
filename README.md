# AWS_VPN-BGP_strongSwan
 
## Architecture Diagrams
![end state architecture](/diagram.jpeg)

The demo is to implement a Dynamic, BGP Based, Highly-Available Site-to-Site VPN by simulating a Hybrid AWS and On-Premises environment - both using AWS. 

The demo consists of 3 cloudformation templates:
- [01.setup-aws-onprem.yml](/01.setup-aws-onprem.yml): This template is to setup AWS and On-Premises VPC with private EC2 instances
- [02.setup-vpn.yml](/02.setup-vpn.yml): This template is to setup public subnet On-Premises to prepare for deploying strongSwan VPN Gateway as an EC2 Instance. Below is the parameters of the template
    - VPC: Choose On-Premises VPC
    - Subnet: Enter any cidr range belonging On-Premises VPC. For example, 192.168.12.0/24
- [03.router.yml](/03.router.yml): This template is to a single EC2 instance as strongSwan VPN Gateway
    - Note down the route table id of private subnet 1 in ON-PREM VPC in Route Tables under VPC Console 
    - Allocate new Elastic IP address under VPC Console and note the IP address and its Allocation ID 
    - Create a Transit Gateway VPN Attachment under VPC Console with these selections:
        - Transit Gateway ID: Select the Transit Gateway created in [01.setup-aws-onprem.yml](/01.setup-aws-onprem.yml)
        - Attachment Type: VPN
        - Customer Gateway: New with the Elastic IP allocated in the above step
        - BGP ASN: 65000
    - Download newly created the VPN configuration in Site-to-Site VPN under VPC Console
    - Below is the parameters of the template:
        - Pre-Shared Key Secret Tunnel: See the downloaded VPN configuration for the “IPSec Tunnel #1” section and “Pre-Shared Key” value.
        - Virtual Private Gateway Outside IP Address: In the “Tunnel Interface Configuration” for tunnel #1, find the “Virtual Private Gateway” in the “Outside IP Addresses” section
        - Customer Gateway Inside IP Address: Find the “Virtual Private Gateway” in the “Inside IP Addresses” section
        - Virtual Private Gateway BGP ASN: See the “BGP Configuration Optons” section of the configuration file for the “Virtual Private Gateway ASN"
        - BGP Neighbor IP Address: See the “BGP Configuration Optons” section of the configuration file for the “Neighbor IP Address”
        - Repeat the same for tunnel 2
        - VPC ID: Choose ON-PREM VPC
        - VPC CIDR Block: Enter the cidr of ON-PREM VPC
        - Subnet ID for VPN Gateway: Select Public Subnet of ON-PREM VPC
        - Subnet ID for Private Routing: Select Private Subnet 1 of ON-PREM VPC
        -   Elastic IP Address Allocation ID: the Elastic IP Allocation ID from the step above
        - Route table of private subnet: the route table id noted from the step above
- Repeat [03.router.yml](/03.router.yml) again for the second instance in private subnet 2 of ON-PREM VPC


## Credits:
-  [Acantril](https://github.com/acantril/learn-cantrill-io-labs/tree/master/AWS_HYBRID_AdvancedVPN)
- [Christopher Kampmeier](https://aws.amazon.com/blogs/networking-and-content-delivery/simulating-site-to-site-vpn-customer-gateways-strongswan/)

