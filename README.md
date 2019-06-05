## AWS ACenterA ECS / EKS

This is a Vue.JS serverless app to help you launch your Container project on AWS. 

This app contains multiple AWS resources, and have few dependencies such as the acentera core serverless app.

Serverless App Dependencies:

   - acentera-prod-core
   - acentera-prod-ecseks-resources

  All launched EC2 instances launch with System Manager configured (and without SSH for security reasons ). To login into your EC2 Instances, you may use the System Manager from AWS Console.
  Each AutoScaling groups will have alarms defined (if SNS is configured). 
  
    - Warning and Critical alarms for disks space for each AutoScaling groups, instead of per VM. Via CloudWatch filtering, you could find the faulty VM's.
    - Updating of ECS Instance Tag to prevent addition of new Containers on that host until it is resolved.
    - Spot instance hooks are automatically enabled on each provisionned EC2 Instances
    - Cluster AutoScaling Up/Down (if enabled), based on number of CPU + Memory remaining in the cluster per set of ASG
    - Distinct Instance criteria, autoscaling.
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

  For ACenterA ECS Resources you need to acknowledge it will create IAM Roles or Resource policies.

  [Serverless Repository](https://console.aws.amazon.com/serverlessrepo/home?region=us-east-1#/available-applications)

  [Serverless App: Create ECS Resources direct link](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:356769441913:applications/acentera-prod-ecseks-resources)

  [Serverless App: Create ECS Solution direct link](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:356769441913:applications/acentera-prod-ecseks)

  ![01 - Serverless App ECS Solution](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/00_acentera_serverless_app.png)

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

  ![Create : Cluster](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/01_acentera_ecs_create_cluster.png)

  ![Create : VPC](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/02_acentera_ecs_create_cluster_vpc.png)

  ![Create : Public Subnets](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/03_acentera_ecs_create_public_zone.png)

  ![Create : Private Subnets](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/04_acentera_ecs_ceate_app_zone.png)

  ![Create : DB (Data) Subnets](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/05_accentera_ecs_create_data_zone.png)

  ![Create : EFS](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/06_acentera_ecs_select_efs_option.png)

  Once you confirm, it would run the generated AWS CloudFormation Template with all the resources.

  ![Create : Cloudformation](https://github.com/ACenterA/acentera-aws-ecseks/raw/master/docs/images/07_acentera_create_cluster_cloudformation.png)

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

## Generic ASG Configuration

  Cloudformation Input fields for the Worker ASG CF Created.

      # The Shared AWS EFS. Leave empty, or specify 
      # an existing filesystem id such as 'fs-xxxxxx'
      filesystem_id: ''
  
      # In which subnet the instances should be placed into.
      # Will find each avaiable subnet with private as tag.
      # See Subnets Tag of acentera:subnet:type 	
      # Fall back to all subnet in the VPC if none are found
      subnet_type: 'app'
  
      # We could specify an static ami instead of using latest.
      ami: 'latest'
      ami_arch: 'x86_64'

      # This value should be identical to the AMI Pipeline one.
      ami_type: 'ECS-BASE-AMI'
  
      # Disk size for the EC2 Instance
      # Note if instance has ephemeral drives, this will only be used for the OS 
      # and not docker.
      ami_root_disk_size: 15
  
      # If we want to enable autoscale or not.
      autoscale: true
  
      # Allowed Values: true, false, "Spot", "Normal"
      autoscale_ratio_respect: true
  
      # Update this date to force a redeployment
      date: '2018-11-12 11:11:05'
  
      # desired size doesnt have impact if ASG have modified its DesiredCapacity
      # due to AutoScaled In or Out Alarms
      desired_instance_count: 2
  
      # Disable AZ rebalance in the event of issues: auto, true, false
      disable_az_rebalance: auto
  
      max_instance_count: 4
      min_instance_count: 2

      # Specify a comma separated list of security groups
      security_groups: 'sg-xxxx,sg-yyy,....'
     
      # Used to define what kind of normal instance type we want (or fallback to)
      normal_instance_type: 'c5d.large'
  
      # Used to define what kind of spot instances we want
      # The logic will use max price of the first instnce in the list
      # and would generate a spot fleet acordingly.
      # If non spot are available it would create regular instances
      spot_instance_types: 'c5d.large,m5.,c5.,r5.'
  
      spot: true

      # If we want only spot instances use 100
      # If we want to have 50% of Non Spot and 50% of Spot instance use 50%
      # Allowed Values :  "0", "25", "50", "75", "100" ]
      # In QA we could go all spot for this worker node group
      spot_ratio: 75
  
      # Spot Alive Ratio upon spot being terminated by AWS
      # if number of spot alive is greater than this value, nothing happens
      # the ASG should re-provision new instances / instances types...
      # If number is lower then the cloudformation stack gets re-triggered to find
      # new spot instance types based on criterias.
  
      # Allowed Values : "10", "25", "33", "50", "65", "70", "80", "100" 
      spot_alive_ratio: 50

      # Use of the lowest X spot instance pool prirce if using spot
      # If only 1 spot instance are availble at the spot price, the transform
      # will set this value to 1 in the nested templates.
      spot_instance_pool_price: 2
   
      # When left at 0, we automatically ajust the price maximum of 80% of the
      # first spot instance type in the list
      spot_bid_price: 0
      spot_max_bid_price: 0
      spot_min_cpu: 0
      spot_min_memory: 0
  
      # If we want EC2 instances to automatically register to a specific TargetGroup on boot.
      # Ie: if we have a deamon service, or for monitoring needs.
      target_group_arns: ''
  
      # Blue/Green deployment will create new resources including separate EBS, and ENI 
      # if using static ENI and EBS to replace the existing instances ...
      # This allows zero-downtime, and ensure that if some instances are offline, 
      # to keep an active quorums.
  
      # Alowed Values : "Blue/Green"
      upgrade_mode: "Blue/Green"
  
      # Used for Task Placements
      worker_sub_type: 'task-placement-subtag'
      worker_type: 'task-placemen-tag'


## License Summary

This code is made available under the Apache license. See the LICENSE file.
