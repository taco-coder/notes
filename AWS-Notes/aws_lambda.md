# Lambda
#### What is Lambda?
Event driven compute service that can run code in response to events.
Events could be changes to data in S3 buckets or DynamoDB table for example.
It runs in response with HTTP requests using AWS API or SDK.
#### Architecture
- Traditional webserver architecture: hit loadbalancer then go to webservers then backend database. Always have resources running (expensive).
- Lambda: API gateway->lambda functions->Database (DynamoDB). Only runs resources when triggered (cheaper).
#### Pricing
- Requests: free for first million then $0.20 per 1 million requests after.
- Duration/Execution time: Depends on amount of memory you allocate to your functions. Charged $0.00001667 for every GB/second used.
#### Version Control
- Lambda has version control integrated so you can rollback to previous versions of your code.
#### What sets Lambda Apart?
- No servers.
- Continuous/automatic scaling out.
- ***Cheap.***
#### Lambda in Action
Lambda can be found in Alexa and Amazon Echo.

---

In the function dashboard, we can modify our lambda function in the IDE below and use the `Designer` to configure a trigger for our lambda function. 