# Cloud Formation
Lets you create templates of code that can create an entire environment with all the resources and you can easily deploy applications across regions.

You can automate your entire infrastructure. It supports YAML or JSON.

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

Easy to google 'AWS cloudformation templates' that you can use.