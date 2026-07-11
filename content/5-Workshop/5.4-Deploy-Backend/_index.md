---
title : "Deploy Backend"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Step 1 — Configure Environment Variables

Create the file `backend/.env` and fill in the resources created in the previous step:

```
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=<access key>
AWS_SECRET_ACCESS_KEY=<secret key>

S3_BUCKET_NAME=ai-marketing-voice
CLOUDFRONT_DOMAIN=<your distribution domain>.cloudfront.net

SES_SENDER_EMAIL=<verified email>

CLOUDWATCH_LOG_GROUP=/ai-marketing-voice/backend
CLOUDWATCH_LOG_STREAM=backend

GOOGLE_CLIENT_ID=<google oauth client id>
DATABASE_URL=sqlite:///./app.db
```

See detailed steps in the subsections: [5.4.1 Prepare Environment](5.4.1-prepare),
[5.4.2 Test VieNeu-TTS](5.4.2-create-interface-enpoint),
[5.4.3 API Testing](5.4.3-test-endpoint),
[5.4.4 Verify CloudWatch & CloudFront](5.4.4-dns-simulation).

Project structure:

```text
AI Marketing Voice
├── backend
│   ├── .env
│   ├── app.db
│   ├── database.py
│   ├── main.py
│   ├── requirements.txt
│   ├── test_bedrock.py
│   ├── generated_audio/
│   │
│   ├── api
│   │   ├── auth.py
│   │   ├── credits.py
│   │   ├── generate.py
│   │   └── voice.py
│   │
│   ├── core
│   │   ├── __init__.py
│   │   ├── logging_config.py
│   │   └── security.py
│   │
│   ├── models
│   │   ├── db_models.py
│   │   └── schemas.py
│   │
│   ├── prompt
│   │   ├── prompt_builder.py
│   │   └── validator.py
│   │
│   └── services
│       ├── bedrock_service.py
│       ├── s3_service.py
│       ├── ses_service.py
│       └── vieneu_service.py
│

```

#### Step 2 — Install Dependencies and Run the Backend Locally

```
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

Open `http://localhost:8000` — you should see:

![Backend running](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/13.png)

#### Step 3 — How the Backend Communicates with Bedrock and VieNeu-TTS

The **script generation** endpoint (`POST /api/v1/generate-script`) builds a prompt from the request (`prompt_builder.py`),
then calls `BedrockService.generate_script()`:

```python
response = self.client.invoke_model(
    modelId=self.model_id,          # anthropic.claude-3-sonnet-20240229-v1:0
    body=json.dumps(body),
    contentType="application/json",
    accept="application/json",
)
result = json.loads(response["body"].read())
raw_text = result["content"][0]["text"]
parsed = json.loads(raw_text)       # model is instructed to return exact JSON format
```

The **voice generation** endpoint (`POST /api/v1/generate-voice`) calls `VieNeuService.generate_voice()` — synthesizing
Vietnamese speech using a locally running model, then uploading to S3 and returning a URL via CloudFront:

```python
tts = self._get_tts()                       # model is lazy-loaded once per process
wav = tts.infer(text=text, voice=voice_id)  # e.g. voice_id = "Xuân Vĩnh"
tts.save(wav, tmp_path)

# Upload to S3, served via CloudFront (or fallback to presigned S3 URL if CLOUDFRONT_DOMAIN is not set)
self.s3.put_object(Bucket=self.bucket, Key=key, Body=data, ContentType="audio/wav")
url = f"https://{cloudfront_domain}/{key}"
```

In parallel, `SESService.send_email()` sends an email whenever a new account is registered, and every
important step is logged via `core/logging_config.py` to both the console and CloudWatch Logs if configured.

Both the script and voice generation endpoints require the user to be logged in, will deduct credits from that
user's balance, and record each transaction as a `CreditTransaction` for easy auditing later.

#### Step 4 — Test the API Directly

Register an account, then call the script generation endpoint (replace `<USER_ID>` with the `id` returned from
`/api/v1/auth/register` or `/api/v1/auth/login`):

```
curl -X POST http://localhost:8000/api/v1/generate-script \
  -H "X-User-Id: <USER_ID>" \
  -H "Content-Type: application/json" \
  -d '{
        "product_name": "Cold-brew coffee bottle",
        "description": "Ready-to-drink cold brew coffee, 350ml glass bottle",
        "price": "45,000 VND",
        "audience": "Office workers, aged 22-35",
        "platform": "TikTok",
        "tone": "Cheerful",
        "duration": "30s",
        "cta": "Buy now"
      }'
```

You will receive a script in JSON format. Then pass the returned `full_script` into the `/api/v1/generate-voice`
endpoint along with a `voice_id` (retrieve the list from `GET /api/v1/voices`) to receive an audio URL via CloudFront.