# Celebrity-Recognition-System
A sytem that recognizes celebrities in a photo using Amazon Rekognition.
Commands
Video Link: https://youtu.be/oHSesteFK5c

Install aws-shell
```
pip install aws-shell
```
Configure
```
aws configure
```
Create a collection on aws rekognition
```
aws rekognition create-collection --collection-id facerecognition_collection --region us-east-1
```
Create table on DynamoDB

```
aws dynamodb create-table --table-name facerecognition \
--attribute-definitions AttributeName=RekognitionId,AttributeType=S \
--key-schema AttributeName=RekognitionId,KeyType=HASH \
--provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
--region us-east-1
```
Create S3 bucket
```
aws s3 mb s3://bucket-name --region us-east-1
```


### To create a celebrity recognition system using Amazon Rekognition through the AWS Console, follow these steps:

### Step 1: Set Up Your AWS Account

1. **Sign in to AWS Console**: Go to [AWS Management Console](https://aws.amazon.com/console/), and either sign in or create a new AWS account.
2. **Create IAM User/Role**: If you don’t already have one, create an IAM user with the necessary permissions to use Rekognition. Go to the **IAM** service and create a new user, assigning the following managed policies:
   - `AmazonRekognitionFullAccess`
   - `AmazonS3FullAccess`
   - (Optional) `CloudWatchFullAccess` for logging

### Step 2: Set Up an S3 Bucket for Image Storage

1. **Create an S3 Bucket**:
   - Go to **S3** in the AWS Console.
   - Click **Create Bucket**, provide a unique name, and choose your preferred region.
   - For simplicity, disable public access (recommended for private testing).
   - Click **Create Bucket**.

2. **Upload Images**:
   - Once your bucket is created, click on it.
   - Click on **Upload** and upload the images you want to recognize.
   - Ensure that the images are in a supported format (e.g., JPEG, PNG).

### Step 3: Use Amazon Rekognition for Celebrity Recognition

1. **Go to Amazon Rekognition Service**:
   - In the AWS Console, search for **Rekognition** and select it.
   - On the Rekognition dashboard, go to the **Celebrity Recognition** section.

2. **Test Celebrity Recognition**:
   - In the console, you’ll have the option to upload an image directly from your local machine or point to an image in an S3 bucket.
   - Click **Choose file** and upload an image from your local machine, or choose an image from your S3 bucket.

3. **Run Recognition**:
   - After selecting the image, click **Recognize Celebrity**.
   - Rekognition will process the image and return the celebrity names (if detected) along with confidence scores and any additional metadata.

### Step 4: Automate the Celebrity Recognition with AWS Lambda (Optional)

To make this system more dynamic, you can integrate AWS Lambda with Amazon Rekognition:

1. **Create a Lambda Function**:
   - Go to **Lambda** in the AWS Console.
   - Click **Create Function** and choose **Author from scratch**.
   - Give your function a name, such as `CelebrityRecognitionFunction`.
   - Choose the execution role created in Step 1.
   - Click **Create Function**.

2. **Configure S3 Trigger**:
   - In your Lambda function, scroll down to **Function Overview** and click **Add Trigger**.
   - Choose **S3** as the trigger and select your S3 bucket.
   - Choose the event type as **Object Created** to trigger the Lambda function when a new image is uploaded.

3. **Write Lambda Code**:
   - In the function editor, write the Python code to handle the S3 event, retrieve the image, and call the Rekognition API for celebrity recognition. Here is an example snippet:

```python
import json
import boto3

rekognition = boto3.client('rekognition')

def lambda_handler(event, context):
    # Get the bucket name and object key from the event
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']
    
    # Call Rekognition to recognize celebrities in the image
    response = rekognition.recognize_celebrities(
        Image={'S3Object': {'Bucket': bucket, 'Name': key}}
    )
    
    # Log and return the response
    print(json.dumps(response, indent=4))
    
    return {
        'statusCode': 200,
        'body': json.dumps(response)
    }
```

4. **Test Lambda Function**:
   - Upload an image to your S3 bucket and monitor the Lambda function’s logs in **CloudWatch** to ensure it’s working as expected.
   - Check the console output to see the recognized celebrities.

### Step 5: Review Results and Adjust

- Based on the test results, you can adjust the parameters in the API calls, such as adjusting thresholds for confidence levels or handling specific cases (e.g., no celebrities found).
  
### Optional: Build a Simple UI (Optional)

To make this more user-friendly, you could build a simple front-end using Amazon API Gateway and AWS Lambda to expose the celebrity recognition functionality as an API, and then build a website or application that users can interact with to upload images and view results.

### Next Steps:

- Explore **Amazon Rekognition Video** to recognize celebrities in videos.
- Explore Rekognition's other features like **facial analysis**, **object detection**, or **content moderation**.

This setup allows you to recognize celebrities in images and automate it using Lambda.
