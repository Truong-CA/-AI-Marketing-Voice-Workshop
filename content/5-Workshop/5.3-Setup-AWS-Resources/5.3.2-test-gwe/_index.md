---
title : "SES & CloudWatch Logs Configuration"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

#### Part 1 — Verify the Sender Email Address in Amazon SES

1. Open the **Amazon SES console** → **Verified identities** → **Create identity**.
2. Select **Email address**, enter the email you want to use for sending welcome emails (e.g., your own email address).
3. Click **Create identity** — AWS will send a confirmation email to that address.
4. Open your inbox and click the confirmation link in the email from AWS.
5. Return to the SES console and confirm that the identity status has changed to **Verified**.

![verified](/images/10.png)
![verified](/images/11.png)

{{% notice tip %}}
By default, new SES accounts are in **Sandbox** mode — you can only send emails to **verified** addresses. For demo/workshop purposes, verifying your own email is sufficient for testing. If you want to send emails to any user without prior verification, you need to go to **Account dashboard** → **Request production access** and fill out the form, which is typically approved within a few hours to a day.
{{% /notice %}}

Record the verified email address — this is the exact value you will enter into the backend's `SES_SENDER_EMAIL` variable.

#### Part 2 — Create a CloudWatch Log Group

The backend has been coded to **automatically create the Log Group** upon startup if it doesn't exist yet (thanks to the `logs:CreateLogGroup` IAM permission granted in the previous step), so you are **not required** to create it manually. However, to ensure correct configuration, you can pre-create and verify it:

1. Open the **Amazon CloudWatch console** → **Log groups** → **Create log group**.
2. Set the name to exactly match the value you will use for the `CLOUDWATCH_LOG_GROUP` variable, for example, `/ai-marketing-voice/backend`.
3. Retention: choose a reasonable value for the demo, such as **3 days** or **1 week**, to avoid accumulating long-term logs that could incur unnecessary storage costs.



#### Double Check
- [ ] SES identity status is **Verified**
- [ ] Recorded the verified email address to use for `SES_SENDER_EMAIL`
- [ ] Log group `/ai-marketing-voice/backend` already exists in CloudWatch
- [ ] Clearly understand SES Sandbox limitations before testing account registration with other emails