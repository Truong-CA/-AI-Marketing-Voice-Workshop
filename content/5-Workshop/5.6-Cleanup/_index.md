---
title : "Cleaning Up AWS Resources"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

To avoid unnecessary AWS charges after completing this workshop, delete all resources in the following order. **Delete the CloudFront distribution before deleting the S3 bucket**, since CloudFront still references the bucket as its origin.

#### 1. Disable and Delete the CloudFront Distribution

1. Open the **Amazon CloudFront Console** and select the distribution you created.
2. Choose **Disable**.
3. Wait until the distribution status changes to **Disabled** (this may take several minutes).
4. Once disabled, select the distribution and choose **Delete**.
5. Navigate to **Origin Access** and delete the **Origin Access Control (OAC)** created in Section 5.3.1, provided it is no longer associated with any distribution.

#### 2. Empty and Delete the Amazon S3 Bucket

```bash
aws s3 rm s3://ai-marketing-voice-<your-name>-demo --recursive
aws s3api delete-bucket --bucket ai-marketing-voice-<your-name>-demo
```

Alternatively, use the AWS Management Console:

**Amazon S3** → Select your bucket → **Empty** → **Delete**

If you previously attached a bucket policy for CloudFront access, remove it first if it prevents the bucket from being deleted.

#### 3. Delete the Amazon CloudWatch Log Group

1. Open the **Amazon CloudWatch Console**.
2. Navigate to **Log groups**.
3. Select `/ai-marketing-voice/backend`.
4. Choose **Actions** → **Delete log group(s)**.

#### 4. Delete (or Keep) the Amazon SES Verified Identity

- If you plan to continue using the verified email address in Amazon SES, you may keep the identity. Verified identities do **not** incur charges when no emails are being sent.
- If you want to remove all workshop resources:

  **Amazon SES Console** → **Verified identities** → Select the identity → **Delete**

#### 5. Delete the IAM User and Policy

1. Open the **AWS IAM Console**.
2. Navigate to **Users** and select `ai-marketing-voice-backend`.
3. Delete the access key used for local development.
4. Detach and delete the least-privilege IAM policy created in Section 5.3.
5. Delete the IAM user.

#### 6. Stop and Delete Compute Resources (If Deployed)

If you deployed the backend to **Amazon EC2**, **AWS App Runner**, or **Amazon ECS** for testing, stop and delete those resources to prevent additional charges.

#### 7. Revoke Amazon Bedrock Model Access (Optional)

Amazon Bedrock uses **on-demand pricing**, meaning simply enabling model access does **not** incur continuous charges. Revoking model access is optional and is typically required only if your organization enforces strict permission management policies.

#### Final Verification Checklist

- [ ] The Amazon CloudFront distribution has been **disabled and deleted**.
- [ ] The Amazon S3 bucket has been deleted.
- [ ] The Amazon CloudWatch Log Group has been deleted.
- [ ] You have decided whether to keep or delete the Amazon SES verified identity.
- [ ] The IAM user and its associated policy have been deleted.
- [ ] No Amazon EC2, AWS App Runner, or Amazon ECS resources are still running.
- [ ] Running `aws s3 ls` and checking the IAM, CloudFront, and CloudWatch consoles confirms that all workshop resources have been removed.

![Cleanup Confirmation](/images/5-Workshop/5.6-Cleanup/cleanup-confirm.png)