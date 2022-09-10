Hands-on Lambda Function and API Gateway

The topics for this hands-on session will be AWS Lambda, function as a service (FaaS). During this Playground we will create two AWS S3 Buckets and using AWS Lambda to synchronize them. In addition, We will show the association between Lambda and API Gateway.

Learning Outcomes

At the end of the this hands-on training, students will be able to;
- Create a S3 bucket.
- Create Lambda Function
- Trigger the Lambda Function with S3 Bucket
- Create a lambda function, that generates a random value and trigger it with API Gateway.

Outline

Part 1 - Prep - Creating AWS S3 Bucket
Part 2 - Creating a Lambda Function and Setting a Trigger Event Part 3 - Creating a Lambda Function and testing
Part 4 - Create a Lambda Function with API Gateway

Part 1 - Prep - Creating a S3 Bucket 

STEP 1 : Prep - Creating S3 Bucket

- Go to S3 menu using AWS console

- Create a bucket of My.source.lambda with following properties,

Bucket name: My.source.lambda
Region: N.Virginia
Block all public access
NACL disabled

- Create another bucket of My.destination.lambda with following properties,

Bucket name: My.destination.lambda
Region: N.Virginia
Block all public access
NACL disabled

Part 2 - Create a Lambda Function and Trigger Event 

STEP 1: Create IAM Role:

- Go to IAM page.
- Go to Roles on the left hand menu and click create role.

Type of Trusted Entity: AWS Service
Use Case: Lambda
Permissions: AmazonS3FullAccess
Name: LambdaS3ReplicaRole

STEP 2: Create Lambda Function

- Go to Lambda Service on AWS Console 
- Functions ----> Create Lambda function
- Select Author from scratch

Name:S3-replica-function
Runtime: Python 3.9
Role: LambdaS3ReplicaRole

STEP 3: Setting Trigger Event

Go to Configuration sub-menu and click AddTrigger on Designer
Trigger Configuration: S3
Bucket: My.source.lambda
Event Type: All object create events

STEP 4: Create Function Code
- Go to the Function Code sub-menu and paste code seen below:

import json
import boto3
# boto3 S3 initialization
s3_client = boto3.client("s3")
def lambda_handler(event, context):
   destination_bucket_name = 'My.destination.lambda'
   # event contains all information about uploaded object
   print("Event :", event)
   # Bucket Name where file was uploaded
   source_bucket_name = event['Records'][0]['s3']['bucket']['name']
   # Filename of object (with path)
   file_key_name = event['Records'][0]['s3']['object']['key']
   # Copy Source Object
   copy_source_object = {'Bucket': source_bucket_name, 'Key': file_key_name}
   # S3 copy object operation
   s3_client.copy_object(CopySource=copy_source_object,Bucket=destination_bucket_name,Key=file_key_name)
   return {
       'statusCode': 200,
       'body': json.dumps('Hello from S3 events Lambda!')
}

- Click "DEPLOY" button

STEP 5: Testing S3 Bucket Synchronization

- Go to S3 Bucket Service
- Select S3 bucket named 'My.source.lambda'
- Upload any files to source bucket
- Go to the S3 bucket named 'My.destination.lambda' and show the uploaded files to the source bucket.

Part 3 - Create a new Lambda Function and test in Postman or real application.

- Create a lambda function
- Go to Lambda Service on AWS Console 
- Functions ----> Create Lambda function
- Select Author from scratch

Name: PassGen
Runtime: Python 3.9
Role: Default

- Go to the Function Code sub-menu and paste code seen below:

import json
import random
def generate_password(length, n_capitals, n_numbers):
    capitals = random.choices('ABCDEFGHIJKLMNOPQRSTUVWXYZ', k = n_capitals)
    numbers = random.choices('0123456789', k = n_numbers)
    letters = random.choices('abcdefghijklmnopqrstuvwxyz', k = length - n_capitals - n_numbers)
    password = capitals + numbers + letters
    random.shuffle(password)
    return str("".join(password))
def lambda_handler(event, context):
    if not 'length' in event or not 'n_capitals' in event or not 'n_numbers' in event:
        return {
            'statusCode': 200,
            'body': json.dumps('Error: Please specify all parameters (length,n_capitals, n_numbers).')
        }
    password = generate_password(event['length'], event['n_capitals'],event['n_numbers'])
    return {
        'statusCode': 200,
        'body': password
}

- Go to the test menu and click on configure tests.
- Click on create new test event and specify the input for your test in the dictionary before clicking create:

{
 "length": 10,
 "n_capitals": 2,
 "n_numbers": 2
}

- Test your Lambda function by clicking on Test:
- You can now use a Python Notebook, your terminal or Postman to test out your API.
- This is how to use API inside code:

import requests
import json
securePasswordGeneratorAPI = "https://bldvaugvlb.execute-api.us-east-1.amazonaws.com/default/password-generator"
data = {
    "length": 10,
    "n_capitals" :2,
    "n_numbers": 2
}
response = requests.post(securePasswordGeneratorAPI, json.dumps(data))
resp = json.loads(response.content)
generatedPassword = resp["body"]
print(generatedPassword)

Part 4 - Create a Lambda Function with API Gateway 

STEP 1: Create Lambda Function

- Go to Lambda Service on AWS Console 
- Functions ----> Create Lambda function
- Select author from scratch

Name: RandomWeather
Runtime: Python 3.9
Role: Default

Configuration of Function Code
- In the sub-menu of configuration go to the "Function code section" and paste code seen below:

from __future__ import print_function
from random import randint
print('Loading function')
def lambda_handler(event, context):
    myNumber = randint(0,4)
    mylist = ['Cloudy', 'Sunny', 'Rainy', 'Snowy', 'Windy']
    return mylist[myNumber]


- Click "DEPLOY" button

STEP 2: Testing your function - Create test event

- Click 'Test' button and opening page Configure test events

- Select Create new test event

Event name: test
Input: {}

- You will see the message Execution result: succeeded(logs) and a random city in a box with a dotted line.

STEP 3 : Create New 'API'

- Go to API Gateway on AWS Console 
- Click "Create API"
- Select REST API ----> Build

Choose protocol: REST
Create : New API
Name: weatherAPI
Endpoint: Regional

STEP 4 : Exposing Lambda via API Gateway

1. Add a New Child Resource

- APIs > FirstAPI > Resources 
- Click on Actions > 'Create Resource' New Child Resource
- Configure as proxy resource: Leave blank Resource 
- Name: weather
- Resource Path: /weather (it automatically appears) 
- Enable API Gateway CORS: Yes
- Click 'Create Resource' button

2. Add a GET method to resource /weather

- Actions > Create Method
- Under the resource a drop down will appear select GET method and click the 'tick'.

3. / GET - Method Execution

Integration type: Lambda Function
Use Lambda Proxy integration: Leave blank
Lambda Region: us-east-1
Lambda Function: RandomWeather
Click 'Save'
Confirm the dialog 'Add Permission to Lambda Function', Click 'OK'

STEP 5: Testing Lambda via API Gateway

- Click GET method under /weather
- Click TEST link in the box labeled 'Client At the bottom of the new view Click 'Test' button
- Under Response Body you should see a random weather. Click the blue button labelled 'Test' again at the bottom of the screen and you will see a new weather appear.
- Test completed successfully.

STEP 6 : Deploy API

- Click Resources select /city
- Select Actions and select Deploy API

Deployment stage: [New Stage]
Stage Name: dev

- it can be seen invoke URL on top like; "https://b8pize8i6e.execute-api.us-east-1.amazonaws.com/dev" and note it down somewhere.
- Enter the Invoke URL into the web browser, add "/weather" at the end and see the generated weather with refreshing the page in the web browser.


