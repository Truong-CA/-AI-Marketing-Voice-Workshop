---
title : "Test Script & Voice Generation API"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

#### Step 1 — Register a Test Account

```
curl -X POST http://localhost:8000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name": "Test User", "email": "test@example.com", "password": "secret123"}'
```

Record the returned `id` — this is the value you will use for the `X-User-Id` header in the following steps. The system uses simple authentication without JWT — see `core/security.py` for more details.

If `SES_SENDER_EMAIL` has been configured and the recipient email is properly verified, check the inbox to confirm that the welcome email was successfully sent via SES.

#### Step 2 — Call the Script Generation API

```
curl -X POST http://localhost:8000/api/v1/generate-script \
  -H "X-User-Id: <registered-id>" \
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

Verify that the response contains all required fields: `title`, `hook`, `body`, `cta`, `hashtags`, `full_script`, and that `credits_remaining` has decreased by exactly 50 from the initial balance (200 credits granted upon registration).

#### Step 3 — Call the Voice Generation API

```
curl -X POST http://localhost:8000/api/v1/generate-voice \
  -H "X-User-Id: <registered-id>" \
  -H "Content-Type: application/json" \
  -d '{"script": "<full_script from the previous step>", "voice_id": "Xuân Vĩnh"}'
```

Open the returned `audio_url` in a browser or use `curl -O` to download and listen, confirming that the content matches the script just generated.

#### Step 4 — Test Error Handling
Try calling `generate-script` again with a non-existent `X-User-Id` or with a missing `product_name` — confirm that the backend returns a clear error (`401` or `400`) instead of crashing.

#### Verification Checklist
- [ ] Registration successful, initial credit balance = 200
- [ ] `generate-script` returns a correctly structured script and deducts exactly 50 credits
- [ ] `generate-voice` returns a playable `audio_url` and deducts credits according to script length
- [ ] Calling without `X-User-Id` returns a `401` error instead of crashing the server