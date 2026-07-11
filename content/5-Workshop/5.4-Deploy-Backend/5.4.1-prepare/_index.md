---
title : "Preparing the Development Environment"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

Before running the backend, create an isolated Python environment to avoid dependency conflicts.

#### Step 1 — Create a Virtual Environment

```bash
cd backend
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
```

#### Step 2 — Install Dependencies

```bash
pip install -r requirements.txt
```

The following are some of the key libraries included in `requirements.txt`:

- `boto3` — Used to interact with Amazon Bedrock, Amazon S3, Amazon SES, and Amazon CloudWatch Logs.
- `vieneu` — Local Vietnamese Text-to-Speech (TTS) model running on CPU.
- `watchtower` — Sends Python application logs to Amazon CloudWatch Logs.
- `fastapi`, `uvicorn`, `sqlalchemy`, `bcrypt`, `google-auth` — Used for API development, database access, authentication, and security.

#### Step 3 — Configure the `.env` File

Create the `backend/.env` file and fill in all required values according to the instructions in **Section 5.4**, including:

- AWS Access Key and Secret Access Key
- Amazon S3 bucket name
- CloudFront domain
- Amazon SES sender email
- Amazon CloudWatch Log Group
- Google Client ID

{{% notice tip %}}
If you leave `CLOUDFRONT_DOMAIN`, `SES_SENDER_EMAIL`, or `CLOUDWATCH_LOG_GROUP` empty, the backend will still start and run normally.

The application automatically falls back to alternative behaviors:
- Audio files are served locally or through presigned Amazon S3 URLs.
- Email sending is skipped.
- Logs are written only to the local console.

This allows you to test individual components of the system without configuring all AWS services before getting started.
{{% /notice %}}