Transcoding Video with S3 and Elastic Transcoder

Introduction

In this hands-on AWS lab, we'll learn how to convert video files to different formats using Lambda, S3, and Amazon Elastic Transcoder.

Elastic Transcoder is a media service designed to be a highly scalable, easy-to-use, and cost-effective way to convert (or "transcode") media files from their source format into versions that will play back on devices like smartphones, tablets, and PCs.

In this lab, we will upload 4K Ultra HD sample videos to an S3 bucket and configure an Elastic Transcoder pipeline to automatically convert them to different formats. We'll set all this up with our own custom Lambda function — written in Python — using the Boto3 SDK.


Log in to the AWS Management Console with the credentials provided on the lab instructions page. Make sure you are using the us-east-1 (N. Virginia) region.

Create S3 buckets
- Create 3 buckets with default settings. Names should look similar to the following:
mybucket-source
mybucket-transcoded
mybucket-thumbnails


Subscribe to an SNS Topic
- In the AWS Management Console, navigate to the SNS service.
- Click Topics in the left sidebar.
- Create the Transcoder topic.
- Create Subscription to topic.
- In the Protocol dropdown box, select Email.
- In the Endpoint box, type an email address you can use to receive the notification.
- Click Create subscription.
- Go to your email application and open the message from AWS Notifications.
- Click the link to confirm your subscription.
- Go back to your SNS browser tab, and open the Transcoder topic to verify that the subscription is confirmed.

Create an Elastic Transcoder Pipeline
- In the AWS Management Console, navigate to the Elastic Transcoder service.
- Click Create a new Pipeline.
Under Create New Pipeline, configure the following settings:
- Pipeline Name: MyPipeline (or any name you like)
- Input Bucket: (Select the bucket with source in its name.)
- IAM Role: Create console default role
Under Configuration for Amazon S3 Bucket for Transcoded Files and Playlists, configure the following settings:
- Bucket: (Select the bucket with transcoded in its name.)
- Storage Class: Standard
Under Configuration for Amazon S3 Bucket for Thumbnails, configure the following settings:
- Bucket: (Select the bucket with thumbnails in its name.)
- Storage Class: Standard
Expand the Notifications menu.
- Select Use an existing SNS topic for all of the events.
- For Select a Topic, choose Transcoder from the dropdown for all of the events.
- Click Create Pipeline.
Note the Pipeline ID. (This is available in the pipeline details and is a different value than the pipeline name.)

Create a Lambda Function
- In the AWS Management Console, navigate to the Lambda service.
- Click Create a function.
Choose the Author from scratch option, and configure the following settings:
- Name: TranscodeVideo (or any name you like)
- Runtime: Python 3.7
- Role: Create a custom role
- Go to IAM service and first create a policy. Paste the policy json code into place. 
Name: Lambda-s3-transcode-log-policy
Create policy.
- Create role using the policy.
Name: Lambda-s3-transcode-log-role
Create role.
- Go back to your Lambda Management Console browser tab, and click Create function.
- On the fucntions main page click to add a trigger event, and select S3.
Scroll down the page to the Configure triggers section, and configure the following settings:
- Bucket: (Select the bucket with source in its name.)
- Event type: All object create events
Click Add to add a trigger to the function.

- Scroll down the page to the function editor, and remove the default code.
- Paste the contents of lambda-function.py source code into the code editor.
- Switch back to your Elastic Transcoder tab, and click the magnifying glass icon to view the details for our pipeline.
- Copy the Pipeline ID to your clipboard, and switch back to your Lambda Management Console browser tab.
- Click Configuration and then scroll down to the Environment variables section,click Edit and set the following variable:
Key: PIPELINE_ID | Value: (Paste the pipeline ID from the pipeline summary page)
Click Save.
Upload Video for Transcoding
- In the AWS Management Console, navigate to the S3 service.
- Open the S3 bucket with source in its name.
- Click Upload.
- Click Add files.
- Select one of the 4K sample video files you downloaded to your local machine.
- Click Upload.

- Open your email application, and check for an email notification from Elastic Transcoder.
- Go back to the S3 Management Console, and open the S3 bucket with transcoded in its name.
- Verify that the 1080p and 720p versions of your uploaded video are present.
- Select one of the transcoded versions of the uploaded video, and verify that it is in the correct format.
- Go back to the S3 home page, and select the S3 bucket with thumbnails in its name.
- Select the .png image that was created, and click Open to verify that the thumbnail was successfully created.

Conclusion
Congratulations, you've successfully completed this lab!