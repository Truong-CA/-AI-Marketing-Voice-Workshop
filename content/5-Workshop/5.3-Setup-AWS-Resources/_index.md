---
title : "AWS Resources & IAM Setup"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Step 1 — Create an S3 Bucket to Store Audio Files

1. Go to the **S3 console** → **Create bucket**.
2. Enter a unique name, for example `ai-marketing-voice`.
3. Enable **Block all public access** (the bucket will be accessed via CloudFront, so direct public access is not required).
4. Click **Create bucket**.

![create bucket](/images/3.png)

*(The steps for creating a CloudFront distribution pointing to this bucket are detailed in section [5.3.1](5.3.1-create-gwe),
and the SES + CloudWatch Logs configuration is covered in section [5.3.2](5.3.2-test-gwe).)*

#### Step 2 — Enable Model Access in Amazon Bedrock

1. Go to the **Amazon Bedrock console** → **Model access**.
2. Request/enable access for **Anthropic Claude 3 Sonnet**.
3. Wait until the status displays **Access granted**.

![bedrock model access](/images/1.png)

#### Step 3 — Create a Least-Privilege IAM Policy

Create a policy that only grants exactly what the backend needs — invoking the Claude 3 Sonnet model, reading/writing objects in your bucket, sending emails via SES, and writing logs to CloudWatch:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "InvokeBedrockModel",
      "Effect": "Allow",
      "Action": "bedrock:InvokeModel",
      "Resource": "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0"
    },
    {
      "Sid": "S3AudioBucketAccess",
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:GetObject"],
      "Resource": "arn:aws:s3:::ai-marketing-voice/*"
    },
    {
      "Sid": "SESSendWelcomeEmail",
      "Effect": "Allow",
      "Action": ["ses:SendEmail", "ses:SendRawEmail"],
      "Resource": "*"
    },
    {
      "Sid": "CloudWatchLogsWrite",
      "Effect": "Allow",
      "Action": ["logs:CreateLogGroup", "logs:CreateLogStream", "logs:PutLogEvents"],
      "Resource": "arn:aws:logs:us-east-1:*:log-group:/ai-marketing-voice/*"
    }
  ]
}
```

#### Step 4 — Create an IAM User or Role and Attach the Policy

1. Go to the **IAM console** → **Users** → **Create user** (for example `ai-marketing-voice-backend`).
2. Directly attach the policy created in Step 3 to this user (least privilege — do not attach any broader managed policies beyond what is necessary).
3. Create an **access key** for this user. When deploying in a real environment, it is recommended to prioritize using an **IAM role** attached to the compute resource instead of a long-lived access key.


#### Verification Checklist
Confirm the following are in place:
- [ ] S3 bucket name has been recorded
- [ ] Bedrock model access shows "Access granted" for Claude 3 Sonnet
- [ ] IAM policy is scoped to exactly the 4 permission groups above (Bedrock, S3, SES, CloudWatch Logs)
- [ ] Access key / role is ready to be used in the backend `.env` file
- [ ] Completed [5.3.1 - Create CloudFront Distribution](5.3.1-create-gwe) and
      [5.3.2 - Configure SES & CloudWatch Logs](5.3.2-test-gwe)