# AWS autoscaling
Autoscaling has 3 components:
1. Groups-Logical component. Webserver group or Application group or Database group etc.
2. Configuration Templates-Groups uses a launch template or launch configuration as a config template for its EC2 instances.
3. Scaling Options-Provide several ways for you to scale your Auto Scaling groups. For example, you can configure a group to scale based on the occurrence of specified conditions or on a schedule. 


**What are my scaling options?**
- Maintain current instance levels at all times.
- Scale manually
- Scale based on a schedule
- Scale based on demand
- Use predictive scaling

### Maintain current instance levels at all times
You can configure your autoscaling groups to maintain a specified number of running instances at all times. To maintain the current instance levels, EC2 Auto Scaling performs periodic health checks on running instances within an `Auto Scaling` group. When EC2 Auto Scaling finds an unhealthy instance, it terminates that instance and launches a new one.

### Scale manually
Manual scaling is the most basic way to scale your resources, where you specify only the change in the maximum, minimum, or desired capacity of your Auto Scaling group. EC2 Auto Scaling manages the process of creating or terminating instances to maintain the updated capacity. 

### Scale based on schedule
Scaling by schedule means that scaling actions are performed automatically as a function of time and date. This is useful when you know exactly when to increase or decrease the number of instances in your group, simply because the need arises on a predictable schedule.

### Scale based on demand
A more advanced way to scale your resources - using scaling policies - lets you define parameters that control the scaling process. For example, lets say that you have a web application that currently runs on 2 instances and you want the CPU utilization of the Auto Scaling group to stay are around 50% when the load on the application changes. This method is useful for scaling in response to changing conditions, when you don't know when those conditions will change. You can set up EC2 Auto Scaling to respond for you. 

### Use predictive scaling
You can also use EC2 Auto Scaling in combination with AWS Auto Scaling to scale resource across multiple services. AWS Auto Scaling can help you maintain optimal availability and performance by combining predictive scaling and dynamic scaling (proactive and reactive) to scale your EC2 capacity faster.



