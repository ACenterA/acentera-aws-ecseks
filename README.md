## AWS ACenterA ECS / EKS

This is a Vue.JS serverless app to help you launch your Container project on AWS. 

This app contains multiple AWS resources, and have few dependencies such as the acentera core serverless app.

Serverless App Dependencies:

   - acentera-prod-core
   - acentera-prod-ecseks-resources


  All launched EC2 instances launch with System Manager configured. To login into your EC2 Instances, you may use the System Manager from AWS Console.
  Each AutoScaling groups will have alarms defined (if SNS is configured). 
  
    - Warning and Critical alarms for disks space for each AutoScaling groups, instead of per VM. Via CloudWatch filtering, you could find the faulty VM's.
    - Spot instance hooks are automatically enabled on each provisionned EC2 Instances
    - Cluster AutoScaling Up/Down (if enabled), based on number of CPU + Memory remaining in the cluster per set of ASG, or if Distinct Instance criteria are not met.
    - Docker Volume Improvement, ( Resize of EBS )
    - Rolling Update of new AMI (without interruption of services)
    - We plan to provide future add-ons such as (Setup a Consul cluster, MongoDB / ES cluster / Splunk / etc ... )
  
  And many more functionalities / features including features that can help you with SOC or PCI...

## Overview
  
  What you will build. In short, everything that you need in AWS to get your systems ready to host containers. 
  You do not need to worry about security, iam roles, permisions etc.. The QuickStart would create everything you need and following
  [Amazon Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-recommended-nacl-rules.html).
  Already have VPC / IAM roles and or VPC/EFS or other resourcse created? No worries, that is supported.

  Here is an overview of the Infrastructure. 

  ![AWS VPC Infrastructure](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/AWS_ECS_EKS_GENERIC_INFRA.png)


## Installation Instructions

1 - Find the application

  For ACenterA Prod Core you need to acknowledge it will create IAM Roles or Resource policies.

  [Serverless Repository](https://console.aws.amazon.com/serverlessrepo/home?region=us-east-1#/available-applications)

  [Serverless Create App direct link](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:356769441913:applications/acentera-prod-ecseks-resources)

  [Serverless Create App direct link](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:356769441913:applications/acentera-prod-ecseks)

  ![01 - Serverless App Core](https://github.com/ACenterA/acentera-aws-core/raw/master/docs/images/01_ACENTERA_CORE_PROD.png)

2 - Find Values in the Stack Output
 
  [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filter=active)

  Find the AWS Account Id (AccountIdD)

  Find the CloudFront URL (WebsiteUrl)

  Navigate to the CloudFront URL you will be asking to proceed with the initialization

3 - App Initialization

  Enter the AccountId to confirm ownership, this would also create a set of Transform Macro in your cloudformation templates.

  ![04 - Serverless App Configuration](https://github.com/ACenterA/acentera-aws-core/raw/master/docs/images/04_ACENTERA_BOOTSTRAP.png)

7 - You should be successfully logged in to the application

8 - You will need to create your first "Cluster Group"
   
   In this section you may create a cluster definition (In its own VPC using AWS's best practices).
   You will be asked to specify the CIDR  of Public, App and DB network, and if you would like to use various
   NAT and a EFS Storage.

9 - New AMI
   
   Return to the main Dashboard using the top left SQUARE minimiazer to return to the main Dashboad.
 
   Click on 'New AMI' in order to create a new AMI that has various ECS optimization. 
   Some of the optimization are:
      1 - mark the node as "no more receiving containers" if the disk storage goes under 2GB of free space.
      2 - receive spot-signal and deactivate the node and drain containers having 'SERVICE_TARGET_GROUP_ARN' as environment variables.
      3 - EBS Rexray DockerStorage resizing, upon resizing of a DockerVolume specified in a CloudFormation up-on the restart of the container the EBS Volume will be resized properly

   
   When you get to the Step about specific Customization using "S3", leave it empty (or clear clear s3 ...).

   Once you launch the AMI the autsocaling group will be reset to 0 after successfull image creation.

10 - Add new Worker Nodes
   
   Once you have created the AMI, you may return to the cluster section from the Main menu,
   And from the left "node" menu, you should create a new set of nodes. The UI have limited cloudformation functionalites.
   

Contact support, and ask for a premium access to allow embedding this app into a SAM template using [nested apps](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessapplication).


## App Outputs

1. `AccountId` - Your aws Account Id.
1. `WebsiteUrl` - Your aws cloudfront https entrypoint.

## License Summary

This code is made available under the Apache license. See the LICENSE file.
