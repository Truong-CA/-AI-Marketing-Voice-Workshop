---
title : "Deploy Frontend & End-to-End Testing"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Step 1 — Configure the Frontend

Project structure:
```text
AI Marketing Voice
│
└── frontend
    ├── .env
    ├── .gitignore
    ├── README.md
    ├── package.json
    ├── package-lock.json
    ├── public
    │   ├── favicon.ico
    │   ├── index.html
    │   ├── logo192.png
    │   ├── logo512.png
    │   ├── manifest.json
    │   └── robots.txt
    │
    └── src
        ├── App.js
        ├── App.css
        ├── App.test.js
        ├── index.js
        ├── index.css
        ├── logo.svg
        ├── reportWebVitals.js
        └── setupTests.js
```

In the `frontend/.env` file, point the application to the running backend:

```
REACT_APP_API_BASE=http://localhost:8000/api/v1
REACT_APP_GOOGLE_CLIENT_ID=<your google oauth client id>
```

#### Step 2 — Install and Run the Frontend

```
cd frontend
npm install
npm start
```

Open `http://localhost:3000`.

![frontend running](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/19.png)

#### Step 3 — Run the End-to-End Flow

![end to end flow](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/ete.png)

1. **Register/Login** on the application (email/password or Google Sign-In) — confirm that the welcome credits (200 credits) are displayed correctly in the sidebar, and check the inbox to confirm the welcome email was received via SES.
2. Go to the **Select Voice** tab, click ▶ to preview several different Vietnamese voices — the first time for each voice will take a few seconds to generate the sample, subsequent times will be faster due to caching.
3. Return to the **Create Script** tab, fill in the product information form and click **Generate Script Now** — confirm that the script (hook/body/CTA/hashtags) is returned and exactly 50 credits are deducted.
4. Click **Generate Audio** on the script just created (using the voice selected in step 2) — confirm that an audio player and download link appear.
5. Play the audio file to confirm the Vietnamese content matches the script correctly.
6. Go to the **Credit History** tab — confirm that all transactions (registration credit grant, script generation deduction, audio generation deduction) are displayed completely and in the correct chronological order.

<video width="100%" controls>
    <source src="https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/demo.mp4" type="video/mp4">
    Your browser does not support video.
</video>

#### Step 4 — Verify on the AWS Side

- **S3 console** → go to your bucket → `audio/` prefix — confirm that a new `.wav` object has been created.
- **Amazon CloudFront** → open the returned `audio_url` and confirm the domain is CloudFront, not a direct S3 URL.
- **Amazon Bedrock console** → check the logs/metrics for Claude 3 Sonnet model invocations (if enabled).
- **Amazon SES console** → **Sending statistics** — confirm there is a successful send corresponding to the welcome email just received.
- **Amazon CloudWatch Logs** → confirm the log group has received all log entries corresponding to the actions just performed on the frontend (registration, script generation, audio generation).
- **IAM console** → confirm the backend is still using only the exact least-privilege policy created in the previous step (no additional permissions required).

#### Step 5 — Test Error Handling
Try entering invalid data (for example, leaving the product name blank, or selecting a platform not in the list of TikTok/Instagram/Facebook/YouTube) and confirm the backend returns a clear `400` error instead of crashing — this validates the logic in `prompt/validator.py`.