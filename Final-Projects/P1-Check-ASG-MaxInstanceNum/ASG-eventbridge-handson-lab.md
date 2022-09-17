Remediating EC2 Autoscaling Group Modifications

Introduction
During this lab, we will learn how to implement a simple remediation solution for correcting changes to a production auto scaling group (ASG) within AWS.

Here is our scenario:

Your company uses a 'multiple environment to a single AWS account' approach for their EC2 deployments. Your DevOps engineering manager has noticed that there have been several outages due to junior engineers accidentally setting the desired capacity of the production ASG to zero (0). She wants to avoid having to manipulate IAM permissions for now and has asked you to put an auto remediation solution in place to auto recover from these mistakes.


- Use Cloudformation template to set up the infrastructure

* Solution
- Verify Existing Infrastructure
- Verify that there are 2 auto scaling groups in the EC2 console. There should be one for production and another for development.
- Verify there is a Lambda function in place called RemediationLambdaFunction
- Create an EventBridge Rule - Event Pattern

Let's create the first part of the event rule.

- Navigate to the EventBridge console.
- Under the Events side menu, select Rules.
- Select the defaul event bus (if not already selected).
- Click on Create Rule.
- Give your rule a name and, if desired, a description.
- Select Event Pattern for the Define pattern portion.
- Now, using the guided menus, create a rule based on EC2 Instance Terminations for EC2 auto scaling groups.
- Choose Pre-defined pattern by service
- For Service Provider choose AWS
- Under Service name we select Auto Scaling
- Event type should be set to Instance Launch and Terminate
- Select Specific instance event(s)
- Set it to EC2 Instance Terminate Successful
- Select the Any group name option.
The JSON should look like the provided sample below:
{
  "source": ["aws.autoscaling"],
  "detail-type": ["EC2 Instance Terminate Successful"]
}

* Create an EventBridge Rule - Select Targets
The last portion is to select our targets.
- Under Select targets we need to select Lambda function under the dropdown (if not there by default).
- For the Function selection we pick our RemediationLambdaFunction from the menu.
- Select Add Target below the initial configuration.
- Tag the resources if desired and then click on Create.

Your rule should be visible in the console with a status of Enabled.

* Test Everything Out
Now that our rule is in place, and our resources are created, let's test the whole solution out.

* Scale Down the Development ASG
- Navigate to the EC2 console.
- Select Auto Scaling Groups
- Find the DevelopmentAutoScalingGroup and select it.
- Under the console menu, select edit and set the desired capacity to zero (0).
- Wait for the EC2 instance to terminate and verify our rule sent an event to EventBridge by ensuring the Lambda function was invoked.
- Navigate to CloudWatch Logs to find and select the Lambda log group and event log stream.
- Verify the logged information is present, detailing the event and ASG name.
- The ASG should be left alone if implemented correctly.

* Scale Down the Development ASG
- Navigate to the EC2 console.
- Select Auto Scaling Groups
- Find the ProductionAutoScalingGroup and select it.
- Under the console menu, select edit and set the desired capacity to zero (0).
- Wait for the EC2 instance to terminate and verify our rule sent an event to EventBridge by ensuring the Lambda function was invoked.
- Navigate to CloudWatch Logs to find and select the Lambda log group and event log stream.
- Verify the logged information is present, detailing the event and ASG name.
- The ASG should be reset to the orignial capacity of one (1) instance.

* Conclusion
Congrats! That's all for this lab. You have implemented a serverless remediation platform for real world events that occur on a daily basis!