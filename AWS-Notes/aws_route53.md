# Route 53
Service to register domain name for your site.

#### Simple routing policy: 
- One record with multiple IP addresses. If you specify multiple values in a record, Route 53 returns all values to the user in a random order.
- Link the domain name to the IP address by going to the domain name settings and creating a record set with Type A and adding all the IPs to the Value box.

#### Weighted routing policy:
- Allows you to split your traffic based on different weights assigned.
- You can set 10% of your traffic to go to us-east-1 and 90% to go to eu-west-1, for example.
- Link the domain name to the IP address by going to the domain name settings and creating a record set with Type A and 1 IP to the value box.
- Under routing policy, set that to weighted then set the weight and set ID (the name).

#### Latency-based routing policy:
- Allows you to route your traffic based on lowest network latency for end user.

#### Failover routing policy:
- Lets you create an active/passive setup.
- For example, a primary site in eu-west-2 and a secondary site in ap-southeast-2.
    

