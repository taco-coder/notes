# AWS autoscaling
You can create an autoscaling group using EC2 images (AMIs).

In the EC2 sidebar scroll down to `autoscaling groups`.

From there, you need to create launch configuration and then you can click on `My AMI`.

Assign the appropriate security group.

You create the autoscaling group using newly created launch configuration.

It automatically creates and spread EC2 instances across all selected AZ.

Scaling policies can change how many instances you can create and the rules for creating new instances.
