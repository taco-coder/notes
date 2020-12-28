# Elastic Load Balancers
A load balancer splits up, or balances, the workload amongst multiple servers. 

#### Application Load Balancers
They are best suited for load balancing of HTTP and HTTPS traffic. They operate at Layer 7 and are application aware. They are intelligent, and you can create advanced request routing, sending specified requests to specific web servers. ALB make use of target groups which are simply a way to bundle up instances, IPs, or lambda functions for whatever reason you may need. For example, if you want a separate group that contains all the US webservers you're using and a separate group for the EU servers.

#### Network Load Balancers
They are best suited for load balancing of TCP traffic where extreme performance is required. Operating at the connection level (Layer 4), Network Load Balancer are capable of handling millions of requests per second, while maintaining ultra-low latencies. 

#### Classic Load Balancers
They are legacy Elastic Load Balancers. You can load balance HTTP/HTTPS applications and use Layer 7-specific features, such as X-Forwarded and sticky sessions. You can also use strict Layer 4 load balancing for applications that rely purely on the TCP protocol. If your application stops responding, the ELB (Classic Load Balancer) responds with a 504 error. This means that the application is having issues. This could be either at the Web Server layer or the Database Layer. X-Forwarding is forward the users IP address through the load balancer to the web server.



Sticky Sessions
: Allow you to bind a user's session to a specific EC2 instance. This ensures all requests from the user during the session are sent to the same instance. You can enable Sticky Sessions for ALBs, but the traffic will be sent at the Target Group Level.


Cross Zone Load Balancing
: Load Balancers can send traffic to EC2 instances in other AZs to optimize the work load.

Path Patterns
: Using a listener with rules to forward requests based on the URL path. Also known as path-based routing. If you are running microservices, you can route traffic to multiple back-end services using path-based routing. For example, you can route general requests to one target group and requests to render images to another target group.

