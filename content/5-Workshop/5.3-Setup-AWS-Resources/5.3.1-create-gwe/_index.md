---
title : "Create CloudFront Distribution"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

This section provides instructions for creating an **Amazon CloudFront distribution** in front of the S3 bucket created in the previous step, to deliver audio files faster instead of using presigned URLs directly from S3.

#### Step 1 — Configure Origin Access Control (OAC)
1. Open the **Amazon CloudFront console** → **Origin access** → **Create control setting**.
2. Enter a name, keep the default **Sign requests** setting, then click **Create**.

Origin Access Control allows CloudFront to read objects in the S3 bucket even while the bucket remains in **Block all public access** mode — users can only access audio via the CloudFront domain and cannot access S3 directly.

#### Step 2 — Create a Distribution
1. Go to **Distributions** → **Create distribution**.
2. **Origin domain**: select the S3 bucket created in [5.3](../).
3. **Origin access control settings**: select the OAC created in Step 1.
4. **Viewer protocol policy**: select **Redirect HTTP to HTTPS**.
5. Keep all other defaults and click **Create distribution**.

![create distribution](/images/6.png)

#### Step 3 — Update the S3 Bucket Policy
After creation, the CloudFront console will suggest a **bucket policy** to paste into the S3 bucket, allowing CloudFront (via OAC) to read objects while still blocking direct public access. Copy that policy and paste it into **S3 bucket → Permissions → Bucket policy**.

#### Step 4 — Record the Distribution Domain
After the distribution transitions from **Deploying** to **Enabled** — which typically takes a few minutes — copy the domain in the format `dxxxxxxxxxxxxx.cloudfront.net`. You will enter this domain into the `CLOUDFRONT_DOMAIN` variable of the backend in the [Backend Deployment](../../5.4-Deploy-Backend/) step.

![distribution domain](/images/9.png)

{{% notice tip %}}
If you do not want to create a CloudFront distribution just yet, the backend will still run normally — simply leave `CLOUDFRONT_DOMAIN` empty and the system will automatically fall back to using presigned URLs directly from S3.
{{% /notice %}}