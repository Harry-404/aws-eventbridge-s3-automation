
# aws-eventbridge-s3-automation

**End-to-end guide: Automatically trigger AWS Lambda when files are uploaded to an S3 bucket, using AWS EventBridge and CloudTrail. Includes setup, code, and cleanup instructions.**

***

## Architecture Overview

- **Source:** Amazon S3 bucket (file uploads)
- **Log:** CloudTrail (monitors S3 API activities)
- **Event Routing:** EventBridge (detects PutObject events and triggers rules)
- **Automation:** Lambda (receives event, logs or processes data)
- **IAM:** Minimal permissions for each service

***

## Prerequisites

- AWS Console access, with permissions for S3, Lambda, EventBridge, CloudTrail
- Basic familiarity with AWS services

***

## Step-by-Step Setup

### 1. Create S3 Bucket

- AWS Console → S3 → Create bucket
- Bucket Name: `my-eventbridge-lab-xyz` (pick a unique name)
- Region: Your preferred AWS Region (e.g., Mumbai `ap-south-1`)
- Block all public access: ENABLED  
- Click **Create bucket**

***

### 2. Enable CloudTrail

- Console → CloudTrail → Create trail
- Name: `eventbridge-trail`
- Storage: Select or create an S3 bucket for logs
- Log management events: **All (Read/Write)**
- Click **Create trail**

***

### 3. Create Lambda Function

- Console → Lambda → Create function
    - Name: `s3EventLogger`
    - Runtime: Python 3.13
    - Permissions: New or existing role with basic Lambda permissions
- Use this code:

```python
import json

def lambda_handler(event, context):
    print("Received event:")
    print(json.dumps(event, indent=2))
    return {
        'statusCode': 200,
        'body': json.dumps('Success')
    }
```

- Click **Deploy**

***

### 4. Set Up EventBridge Rule

- Console → EventBridge → Rules → Create rule
    - Name: `s3PutObjectRule`
    - Event bus: default
    - Rule type: Event pattern
- Pattern (JSON):

```json
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["s3.amazonaws.com"]
  }
}
```
- Target: Lambda → `s3EventLogger`
- Save

***

### 5. Test Workflow

- Upload any file to your S3 bucket
- Check CloudWatch Logs for Lambda events
- Check CloudTrail and EventBridge rule metrics

***

### 6. Cleanup

- Delete the S3 bucket
- Remove Lambda function, EventBridge rule, and CloudTrail trail

***

## Best Practices

- Always grant **least privilege** to roles.
- Keep S3 public access blocked for security.
- Delete unused resources to avoid charges.

***

## Contact

- [LinkedIn](https://www.linkedin.com/in/hiranmaya-biswas-505a1823a/)
- [GitHub](https://github.com/Harry-404)

