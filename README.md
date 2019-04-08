## AWS ACenterA ECS / EKS

This is a Vue.JS serverless app to help you launch your Container project on AWS. 

This app contains multiple AWS resources, and have few dependencies such as the acentera core serverless app.

Serverless App Dependencies:

   - acentera-prod-core
   - acentera-prod-ecseks-resources

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

  Enter the AccountId to confirm ownership.

  ![04 - Serverless App Configuration](https://github.com/ACenterA/acentera-aws-core/raw/master/docs/images/04_ACENTERA_BOOTSTRAP.png)

7 - You should be successfully logged in to the application

Contact support, and ask for a premium access to allow embedding this app into a SAM template using [nested apps](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessapplication).

## App Outputs

1. `AccountId` - Your aws Account Id.
1. `WebsiteUrl` - Your aws cloudfront https entrypoint.

## License Summary

This code is made available under the Apache license. See the LICENSE file.
