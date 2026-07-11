---
title : "Prerequisites"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Set Up Your AWS Account and Services

- An AWS account with billing enabled.

- **Enable access to the Claude 3 Sonnet model** in **Amazon Bedrock**. The approval process may take a few minutes.
- Choose an AWS Region that supports Amazon Bedrock (for example, `us-east-1`).
- An email address that you can access and verify in **Amazon SES** (used as the sender address for welcome emails).

#### Required Local Development Tools

- Python 3.10+ and `pip`
- Node.js 18+ and `npm`
- AWS CLI v2, configured using `aws configure`
- Git
- At least **2 GB** of available disk space and a stable Internet connection, as **VieNeu-TTS** downloads its model from Hugging Face the first time it runs.

#### AWS Permissions to Be Created in the Next Step

- An IAM user or IAM role with the minimum required permissions:
  - `bedrock:InvokeModel` (restricted to the ARN of the Claude 3 Sonnet model)
  - `s3:PutObject`, `s3:GetObject` (restricted to the project's S3 bucket)
  - `ses:SendEmail`, `ses:SendRawEmail`
  - `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`

#### Project Source Code

Clone or download the project repository (backend and frontend) so that you can configure and run it locally or deploy it to AWS.

Example:

```bash
git clone https://github.com/Truong-CA/AI-ContentCreator
cd "AI Marketing Voice"
```

![GitHub Repository](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/12.png)