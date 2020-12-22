# IAM Credential Report
Shows when passwords are used
shows when access keys were made and last used

Go into IAM and you can download from tab 'credential report'

# IAM
Under services->security

can create groups with appropriate permissions and add users to groups (best practice for cleaner management)

BEST PRACTICE: use root account to make IAM accounts for every user, even admin. Should not use root account for anything other than creating and managing user accounts

can enable MFA for root account; if you lose access to MFA app then you need to contact AWS support

Tons of different job functions and permissions for every type of user account

when granting programmatic access, you only see the credentials once so be sure to download the .csv not to lose the info

under policies, you can see the json notations of the policies 

can create password policy: length, special characters, reuse, and how often it needs to be updated

if Access key is lost for user, we can deactivate the old key and create a new key for them

can create Roles to allow one AWS service to use another AWS service ie allowing EC2 to have admin access to S3


# Directory Service
    Family of managed services that connect AWS resources with on-premises Active Directory (AD). Standalone directory in the cloud that uses existing corporate credentials
    
    ### Active Directory:
        -On-premises directory service
        -hierarchical database of users, groups, computers
        -can apply group policies


# Policies
    ### Amazon Resource Name (ARN):
        -Define the name for every service available through AWS
        -ARNs begin with: 
'''json
                arn:partition:service:region:account_id:[resource 
                                                         OR 
                                                         resource_type/resource 
                                                         OR 
                                                         resource_type/resource/qualifier 
                                                         OR 
                                                         resource_type/resource:qualifier
                                                         OR
                                                         resource_type:resource
                                                         OR
                                                         resource_type:resource:qualifier
'''
    ### Examples of ARN:
        1. arn:aws:iam::123456789012:user/mark
        2. arn:aws:s3:::my_awesome_bucket/image.png
        3. arn:aws:dynamodb:us-east-1:123456789012:table/orders
        4. arn:aws:ec2:us-east-1:123456789045:instance/*

        **multiple colons following each other are omitted values because ARN is global. Example 1 is a user account, so region is not needed.**
        Example 2 is an S3 bucket, no region or account id are associated with it.

    Policies are JSON documents that define permissions. 2 types of policies.
        -Identity policies
        -Resource policies
    Policies are just a list of statements in a JSON file:
''' json
        {
            "Version":"2020-12-21"
            "Statement": [
                {
                    //statement 1
                },
                {
                    //statement 2
                },
                {
                    //statement 3
                },           
            ]        
        }

        Each statement matches an AWS API request
        Example:
        {
            "Version":"2020-12-21"
            "Statement": [
                {
                    "Sid": "SpecificTable", //human readable statement to let you know what the statement is for
                    "Effect":  "Allow",     //Effects will always be Allow or Deny
                    "Action": [
                        "dynamodb:BatchGet*",
                        "dynamodb:DescribeStream",
                        "dynamodb:DescribeTable",
                        "dynamodb:Get*",
                        "dynamodb:Query",
                        "dynamodb:Scan",
                        "dynamodb:BatchWrite*",
                        "dynamodb:CreateTable",
                        "dynamodb:Delete*",
                        "dynamodb:Update*",
                        "dynamodb:PutItem",
                    ] //end action
                    "Resource": "arn:aws:dynamodb:*:*:table/MyTable"
                },  //end statement 1         
            ]  //end statement block     
        }  //end file          
'''
        2 types of policies: AWS managed policies and customer managed policies
            -AWS managed policies are created by AWS for convenience
            -Customer managed policies are policies made by you and can be edited for specific purposes
        

        
        



