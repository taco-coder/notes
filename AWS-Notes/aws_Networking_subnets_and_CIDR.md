# AWS network IP and subnets and CIDR
AWS uses a CIDR block addresses.


EX: `10.0.1.0/24`

`10.0.1` represents the network address.


`.1` at the end respresents the host on that network.
---
The subnet mask lets you know which bits are used for the address:


`255.255.255.0`

Each chunk is 8 bits so a possible 256 addresses -1 for network and -1 for broadcast for
a total of 254 host addresses. Address 0 and 255 are reserved.


`10.0.1.0/24` CIDR masks don't have to be divisible by 8


EX: Could take the 254 hosts addresses on `10.0.1.0/24` and 
split them into 2 subnets: 

| CIDR: `10.0.1.0/25` | CIDR: `10.0.1.128/25` |
| Subnet: `255.255.255.128`	| Subnet: `255.255.255.128` |
| Host addresses: 1-127* | Host addresses: 129-254* |

*Staggered by 1 because of the reserved hosts.



