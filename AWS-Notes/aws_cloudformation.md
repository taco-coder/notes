# Cloud Formation
Lets you create templates of code that can create an entire environment with all the resources and you can easily deploy applications across regions.

You can automate your entire infrastructure. It supports YAML or JSON.
Some terms for used in cloudformation:
- Stack: A collection of AWS resources that you manage as a single unit. The stack is created when you give the CloudFormation service your template.
- Template: The text file you write to outline what we want cloudformation to provision. We can create and update stacks. 
- Stack Policy: IAM style policy which governs what can be changed and who can change it.


Deleting the stack  will delete the resources tied to that stack as well.
### Example Cloudformation Template (YAML)
```
AWSTemplateFormatVersion: "2010-09-09"
Description: "Template to create an EC2 instance"
Metadata:
 Instances:
  Description: "Web Server Instance"

Parameters: #input values
 EnvType:
  Description: "Environment type"
  Type: String
  AllowedValues:
   - prod
   - test

Conditions:
 CreateProdResources: !Equals [ !RefEnvType, prod ]

Mappings: #e.g. set values based on a region
 RegionMap:
  eu-west-1:
   "ami":"ami-0bd1d6c15a40392c"
        
Transform: #include snippets of code outside the main template
 Name: 'AWS::Include'
 Parameters:
  Location:'s3://MyAmazonS3BucketName/MyFileName.yaml'

Resources: #the AWS resources you are deploying
 EC2Instance:
  Type: AWS::EC2::Instance
  Properties:
   InstanceType: t2.micro
   ImageId: ami-0bd1d6c15a40392c
```

Easy to google 'AWS cloudformation templates snippets' that you can use.
#### Template Anatomy
- Parameters
: Allow the passing of variables into the template via the UI, CLI, or API.
- Mappings
: Allow processing of hashes (arrays of key/value pairs) by the template.
- Resources
: Where your resources are actually declared.
- Outputs
: Results from teh template.
### Intrinsic Functions
Cloudformation provides functions to assist in assigning values to template properties that are not available until runtime. 


FindInMap YAML Example
```
Mappings:
 RegionMap:
   us-east-1:
    HVM64: ami-id#
    HVMG2: ami-id#
   us-west-1:
    HVM64: ami-id#
    HVMG2: ami-id#
Resources:
 myEC2instance:
  Type: "AWS::EC2::Instance"
  Properties:
   ImageId: !FindInMap 
    - RegionMap
    - !Ref 'AWS::Region' 
    - HVM64
   InstanceType: m1.small
```

Some other functions:
- Base64 encoding to EC2
```
YAML:
!Base64 valueToEncode
```
- Cidr returns an array of CIDR address blocks whose number is dependent on the *count* value, from 1 to 256
```
YAML:
!Cidr [ ipBlock, count, cidrBits ]
```
- GetAtt returns the value of an attribute from a resource in the template
```
YAML:
!GetAtt logicalNameOfResource.attributeName
```
- GetAZs returns an array that lists AZs for a specified region
```
YAML:
!GetAZs region
```
- ImportValue returns the value of an output exported by another stack. Typically used to create cross-stack references.
```
YAML:
!ImportValue sharedValueToImport
```
-Join appends a set of values into a single value, separated by the specified delimiter
```
YAML:
!Join [ delimiter, [comma-delimited list of values ] ]
```
- Select returns a single object from a list of objects by index
```
YAML:
!Select [ index, listOfObjects ]
```
- Split splits a string into a list of string values so that you can select an element from the resulting string list
```
YAML:
!Split [delimiter, sourceString]
```
- Sub substitutes variables in an input string with values that you specify 
```
YAML:
!Sub
 - String
 - {Var1Name: Var1Value, Var2Name: Var2Value}
 ```
 - Transform specifies a macro to perform custom processing on part of a stack template
 ```
 YAML:
 !Transform { "Name" : macroName, "Parameters" : { key : value, ... } }
 ```
 - Ref returns the value of the specified parameter or resource
 ```
 YAML:
 !Ref logicalName
 ```
 - Condition functions
 ```
 Fn:And
 Fn:Equals
 Fn:If
 Fn:Not
 Fn:Or
 ```

 ### Wait Conditions
 - DependsOn is used for controlling creation order within CloudFormation
 ```
 Resources:
  MyEC2Instance:
   Type: AWS::EC2::Instance
   Properties:
    ImageId: ami-id#
    DependsOn: GatewayToInternet

GatewayToInternet:
 Type: AWS::EC2::VPCGatewayAttachment
 Properties:
  VpcId:
   Ref: VPC
  InternetGatewayId:
   Ref: InternetGateway
```
While this lets CloudFormation that the resource is available, it isn't necessarily ready to be used which could cause errors. We need to use a creation policy to prevent a resource status from reaching `create complete` until Cloudformation receives a specified number of success signals or the timeout period is exceeded.
```
CreationPolicy:
 ResourceSignal:
  Count: '3'
  Timeout: PT15M
```
Wait conditions allow you to coordinate stack resource creation with **other** configuration actions that are external to the stack and also allows you to track the status of a configuration process. Wait condition handlers are a resource with no properties which generates a signed URL which can be used to communicate SUCCESS or FAILURE.

Wait conditions have 4 components:
1. They DependOn the resource(s) you are waiting on.
2. A Handle property references the above handle.
3. They have a response timeout.
4. They have a 'count', if none is specified the default is 1.

##### Wait Condition Handle
```
WaitHandle:
 Type: AWS::CloudFormation::WaitConditionHandle

WaitCondition:
 Type: AWS::CloudFormation::WaitCondition
 DependsOn: "WebServerGroup"
 Properties:
  Handle:
   Ref: "WaitHandle"
  Timeout: "300"
  Count:
   Ref: "WebServerCapacity"

WebServerGroup:
 Type: AWS::Autoscaling::AutoscalingGroup
 Properties:
  LaunchConfigurationName:
   Ref: "LaunchConfig"
  MinSize: "1"
  MaxSize: "5"
  DesireCapacity:
   Ref: "WebServerCapacity"
  LoadBalancerNames:
   - 
    Ref. "ElasticLoadBalancer"
```
### Deletion Policies
Deletion policies handle what happens to each resource when a stack is deleted. The policy value can be one of: Delete (Default), Retain, or snapshot.
```
myS3Bucket:
 Type: "AWS::S3:Bucket"
 DeletionPolicy: Retain
 ```
 ### Stack Updates
 ##### What happens when we update a stack?
 - The stack policy is checked.
 - The changes are orchestrated.

 ##### Example Stack Policy
 ```
 {
     "Statement" : [
         {
             "Effect" : "Allow",
             "Action" : "Update:*",
             "Principal" : "*",
             "Resource" : "*"
         }
         {
             "Effect" : "Deny",
             "Action" : "Update:*",
             "Principal" : "*",
             "Resource" : "LogicalResourceId/ProductionDatabase"
         }
     ]
 }
 ```
 The absence of a stack policy allows all updates. Once a stack policy is applied, it can't deleted. Once a policy is applied, by default ALL objects are protected and Update:* is Denied. To explicitly allow `Update:*`, you need this policy.
 ```
 {
     "Statement" : [
         {
             "Effect" : "Allow",
             "Action" : "Update:*",
             "Principal" : "*",
             "Resource" : "*"
         }
     ]
 }
 ```
The change a resource undergoes during an update is dependent on the resource or property. An update can impact a resource in four ways:
- No interruption.
- Some interruption.
- Replacement.
- Delete.

AWS::EC2::Instance - AvailabilityZone: changing it causes a replacement.
AWS::EC2::Instance - InstanceType: changing the type has some interrupts because CloudFormation is rebooting it and starting up that new instance type.
AWS::DynamoDB::Table - ProvisionedThroughput: changing the throughput has no interruption at all


### Change Sets
Change Sets allow you to preview how your changes will impact your stack and resources, show you if changes will delete or replace critical resources, and let you make changes only when you execute the change set (no need to commit to changes; you can just back out).

### Custom Resources
Custom resources enable you to write custom provisioning logic in templates. 