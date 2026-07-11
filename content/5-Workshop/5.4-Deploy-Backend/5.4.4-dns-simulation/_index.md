---
title : "Verifying CloudWatch Logs & CloudFront"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

After testing the APIs in [Section 5.4.3](../5.4.3-test-endpoint), this step helps verify that application logs are successfully sent to Amazon CloudWatch Logs and that generated audio files are correctly delivered through Amazon CloudFront and Amazon S3.

#### Step 1 — Verify CloudWatch Logs

1. Open the **Amazon CloudWatch Console** → **Log groups** → select the log group you configured (for example, `/ai-marketing-voice/backend`).
2. Open the most recent log stream (by default, `backend`, based on `CLOUDWATCH_LOG_STREAM`).
3. Confirm that log entries corresponding to the actions you tested are displayed. For example:

   ```text
   New user registered: test@example.com
   User test@example.com generated a marketing script: Cold Brew Coffee (-50 credits)
   User test@example.com generated voice audio using Xuan Vinh (-25 credits)
   ```

#### Step 2 — Verify the Audio File in Amazon S3

1. Open the **Amazon S3 Console**.
2. Navigate to your S3 bucket and open the `audio/` prefix.
3. Confirm that a new `.wav` file has been uploaded after calling the `generate-voice` API.

![Object in Amazon S3](/images/17.png)

#### Step 3 — Verify Audio Delivery Through CloudFront

If `CLOUDFRONT_DOMAIN` has been configured, the `audio_url` returned by the API should have the following format:

```text
https://dxxxxxxxxxxxxx.cloudfront.net/audio/<file>.wav
```

instead of a presigned Amazon S3 URL.

Open the URL directly in your browser to verify that:

- The audio file plays successfully.
- The original Amazon S3 object **cannot** be accessed directly through its S3 URL because the bucket blocks public access, and only Amazon CloudFront using **Origin Access Control (OAC)** is authorized to retrieve the object.

#### Verification Checklist

- [ ] Log entries appear correctly in Amazon CloudWatch Logs for each action (user registration, script generation, and audio generation).
- [ ] The generated `.wav` file is stored in the `audio/` folder of the Amazon S3 bucket.
- [ ] The returned `audio_url` uses the Amazon CloudFront domain instead of a presigned Amazon S3 URL and is accessible.
- [ ] Direct access to the original Amazon S3 object URL is denied because **Block Public Access** is enabled.