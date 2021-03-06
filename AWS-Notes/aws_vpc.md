# VPC
Virtual Private Cloud lets you provision a logically isolated section of AWS cloud. You control the IP address range, subnets, and config of network gateways and route tables between subnets. For example, you can create a public facing subnet for your webservers and place backend facing systems such as DBs and application servers in private facing subnets with no Internet access.

#### Default VPC:
- Default VPC is user friendly, allowing you to immediately deploy instances.
- All subnets in default VPC have a route out to the Internet.
- Each EC2 instance has both a public and private IP address.

#### VPC Peering:  
- Allows you to connect one VPC with another via a direct network route using private IP addresses.
- Instances behave as if they were on the same private network.
- You can peer VPCs with other AWS accounts as well as with other VPCs in the same account.
- Peering is in a star configuration: ie 1 central VPC peers with 4 others. NO TRANSITIVE PEERING.
- You can peer across regions.

        


# Creating a custom VPC
### 1. Under the VPC dashboard side menu, go to `Your VPCs`.
- It'll display the default VPC which has 3 subnets linked to it (found under the 'Subnets' tab in the side menu).

### 2. When back at `Your VPCs`, click `Create VPC`.
- Assign an IPv4 CIDR address to your VPC; the largest is `10.0.0.0/16`.
- Tenancy can stay default unless your application absolutely requires dedicated VPC (more expensive).
- By default, our new VPC has no subnets or Internet gateways created for it. We do, however, have a route table, Network ACL, and security group.

### 3. Under the `Subnets` menu option, click `Create Subnet`.
- Under VPC, select the ID of the VPC you just created.
- Under Availability Zone, the zones are randomized (your `us-east-2a` != my `us-east-2a`).
- Assign an IPv4 CIDR address to the subnet eg `10.0.1.0/24`.
    - IPv4 block sizes must be between a /16 netmask and /28 netmask and can be the same size as the VPC.
- Upon creation, you will see you only have 251 available IPv4 addresses under this subnet and that's because Amazon reserves some addresses for DNS(`10.0.*.2`), VPC router(`10.0.*.1`), Network Address(`10.0.*.0`), Network broadcast address(`10.0.*.255`), and one for a future, unknown reason (`10.0.*.3`).
- Subnets by default don't have public IP addresses.
    - Select the subnet and under `Actions` select `Modify auto-assign IP settings` and enable auto-assign IPv4.
    - We now have a public IP for the subnet and can launch EC2 instances on it and they will have a public IP address.
    
### 4. In the VPC dashboard, select Internet Gateway from the side menu and select "Create internet gateway". An internet gateway is a virtual router that connects a VPC to the internet.
- Create a name for the gateway then hit `Create`.
- The new gateway will show as `detached`.
    - Select the gateway and under `Actions` select `Attach to a VPC`.
- We can only have one internet gateway per VPC.
    
### 5. We need to configure our route table with the gateway. Select `Route Tables` from the sidebar menu.
The newly created subnets are automatically associated with the main route table and if we were to create a route out to the internet in the main route table, any subnets we create would be publicly available. For security concerns you may need 2 separate subnets, a public and private. A good practice is to have the main route table private and create a separate public route table that we need to explicitly assign subnets.
- Select `Create Route Table` and give it a name and select the VPC we wish to use this table with.
- In the newly create public route, select the `Routes` tab below and select `Edit Routes`.
    - Click `Add route` and to create a route out to the internet set destination as `0.0.0.0/0`.
    - Under target, select the `Internet Gateway`.
    - To allow IPv6 addresses to the destinations, add new route and set destination as `::/0`
- To associate a subnet with this publicly available route, select the `Subnet Associations` tab and click `Edit subnet associations`.
    - Associate the subnet(s) that were given public IP addresses.
        



# NAT instances and NAT gateways
Network Address Translation (NAT) is a way to enable private (no route out to the internet per subnet rules) EC2 instances to still be able to download software and run updates without moving them to a public subnet. NAT instances are being phased out because they only cover individual EC2 instances whereas NAT Gateways covers entire subnets; as such, these notes will only cover NAT Gateways.
    
### Under VPC sidebar menu, select `NAT Gateway` and click `create NAT Gateway`.
1. Select the subnet that we determined would be our public subnet.
2. We will most likely need to create a new elastic ip address that attaches to our NAT Gateway.
3. We create our NAT Gateway and we need to edit our Route Table to work with the gateway.
4. Select our Main route table (recall the one that didn't have a route out to the internet) and edit its routes to add a new route.
    - Set destination `0.0.0.0/0` and set target as `NAT Gateway` and hit save.
5. NAT gateways can take some time to setup; you can view the status in the NAT Gateways menu.
6. When this is complete, you should be able to update and install software to your private EC2 instance.



# Network Access Control List vs Security Groups
You can find Network Access Control List under the `Network ACL` sidebar menu option in the VPC dashboard. It'll be populated with default NACLs from our subnets. Selecting one of the NACLs will show you the Inbound and Outbound rules at the bottom.
    
### We can create a new NACL and associate it with our custom VPC.
By default all the permissions will be Denied and no subnets are associated with it.
### 1. Under `Subnet associations` tab, we can associate subnets to our new NACL.


Since the default webtraffic permissions are set to Denied, trying to access our public IP from the web will result in a page time out (see "Creating an EC2 Webserver" in EC2 notes).

### 2. We need to edit inbound rules to allow internet traffic.
- When adding rules, port `80` is for http, port `443` is for https, and port `22` is for ssh. Source `0.0.0.0/0` is any device in the world.
    - We need to edit outbound rules as well to allow the site to communicate with devices.
- Add port `80` for `http`, add port `443` for `https`, add port `1024-65535` for ephemeral ports.
    - Ephemeral ports are used by TCP and act as the port on the server end of communication. Allows continued communications with a client that initially connected to one of the servers. 
### 3. The ports are needed on both inbound and outbound rules to run software updates and installs from ssh.


Rules are weighted in chronological order; rule 1 > rule 4 etc. Denies and allows should be ordered mindfully as to not counteract one another.






# VPC Flow Logs
VPC flow logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces within your VPC. Flow log data is stored using Cloudwatch Logs or S3.
1. In the Cloudwatch dashboard, under `Logs` you can select `Create Log Group`.
2. Under the VPC dashboard, go to `My VPCs` and select the VPC. Under `Actions` you can select `Create Flow Log`.
3. (Optional) You can create a new IAM role to manage the flow logs.

The newly created logs can be found under `Logs` in Cloudwatch if Cloudwatch was the selected destination
                                                                                                      




# Direct Connect
Direct Connect is a cloud service that allows you to establish a dedicated network connection from your premises to AWS. Using Direct Connect, you can establish private connectivity between AWS and you datacenter/office/colocation environment.

# Global Accelerator
Global Accelerator is a service where you create accelerators to improve availability and performance of your applications for local and global users. Directs traffic to optimal endpoints over the AWS global network. 

    
    
# VPC Endpoints
VPC Endpoints allow you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway. Traffic between your VPC and other services does not leave the Amazon network. They are horizontally scaled, redundant, and highly available VPC components.


2 types of endpoints:
- Interface endpoints: an elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service.
- Gateway endpoints: similar to interface endpoints. Works like network gateways. It can be setup through the VPC dashboard under `Endpoints`.




# PrivateLink
It's a way to expose a service VPC to thousands of customer VPCs. Doesn't require peering, route tables, internet, etc. Requires Network Load Balancer on the service VPC and an ENI (Elastic Network Interface) on the customer VPC.

