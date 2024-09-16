How to do set up customized email notifications for Available volumes in AWS.
Overview
Cloud cost control is one of the top goals for customers across all sectors and industries. With respect to the AWS EBS storage service, unused resource expenses  may be incurred if the lifecycle of volumes is not fully observable. Hence, Amazon EBS volumes that are unused or are forgotten about, add to AWS charges.
In this article, we'll show you how to utilize AWS Lambda, Amazon EventBridge, and AWS SNS to discover EBS volumes that are available state and disconnected from an EC2 instance by receiving alerts through email notifications. This strategy will aid in cost reduction and cost optimization.
Prerequisites:
Create IAM role includes SNS publish, EBS volume describe, list and basic lambda execution permissions.
Setup SNS Notification and email subscription.
create the lambda function.
create EventBridge schedule.

Steps walkthrough
Create IAM role
Visit the IAM Dashboard using the Amazon Management Console. On the IAM dashboard, select role and create the role with basic permissions for EC2, SNS and Cloudwatch logs 
once you complete the create the role get into the next step.
Create Simple Notification Service
To receive email notifications, we need one subscribed AWS SNS topic. We will utilize the SNS topic ARN in Lambda code.
In the AWS Management Console, navigate to Simple Notification Service and create a topic. Provide a Name and accept all the default settings. Note the topic's ARN.
For the type, I would be selecting Standard. Then just enter name and display name for your topic.
Create Subscription for Email notification
Here, for protocol, select Email
For Endpoint give email address where you want to receive the alert email notification.
If we want to change the protocol to any other one, select the option from drop down menu.
once click the create subscription get this page
conform subscription email sent to the email address you entered.
once SNS creation is done, we are going to next step
 Create Lambda Function
In Amazon Management console, create lambda function
Navigate to the Lambda console and create a function with following basic details as shown in the following
 . For Function name, enter unused-volumes
For Runtime, from the dropdown list, select Python 3.7.
Under Permissions:
For Execution role, from the dropdown list, select Use an existing role.
For Existing role, select the role created in the previous step for the Lambda function

Then open the code editor and write the code, using the below code and correct SNS topic ARN and then select Deploy.
```import boto3
def lambda_handler(event, context):
    ec2_client = boto3.client('ec2')
    sns_client = boto3.client('sns')
    volumes = ec2_client.describe_volumes()
    sns_arn = '<SNS Topic ARN>'
    
    unused_vols = []
    for volume in volumes['Volumes']:
        if len(volume['Attachments']) == 0:
            unused_vols.append(volume['VolumeId'])
            print(volume)
            
    
    email_body = "##### Unused EBS Volumes ##### \n"
    
    for vol in unused_vols:
        email_body = email_body + f"VolumeId = {vol} \n"
       
    
    # Send Email
    
    sns_client.publish(
        TopicArn = sns_arn,
        Subject = 'Unused EBS Volumes List',
        Message = email_body
    )
    print(email_body)
```
Now lambda function is ready for execution.
Create EventBridge Scheduler
In EventBridge console select the create rule,
Here, give the name unused-ebsvolume-lambda-trigger ang select Ruletype Schedule
In schedule pattern if there is need for one time we have to select one-time schedule or every time you repeat the schedule time select Recurring schedule.
In the Targets details, select the AWS Lambda option and select our lambda function which we build in the earlier step.
In permission section for execution role select create new role this schedule or you have already existing role select Use existing role.
Once you click the create schedule every day 10a.m scheduler trigger to lambda function and lambda function check the volume state get email alert from SNS.

Conclusion
In the blog, instructions were provided on receiving email notifications about a list of unused EBS volumes, enabling review for further action and deletion if unnecessary to reduce the cost of the monthly Amazon bill.

https://medium.com/p/6425b2119f5f/edit
