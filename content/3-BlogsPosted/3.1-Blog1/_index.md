---
title: "Blog 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# A Costly Lesson in IAM: From Root Account to Least Privilege

## 1. How it started

When I was first learning AWS, I did everything with the root account because... it was convenient. Creating buckets, spinning up EC2 instances, calling Bedrock — all logged in with the main account's email and password. Back then I figured it was simple: "Use root for speed now, sort out IAM later once the project gets bigger."

Then I came across a real case on Reddit: someone accidentally committed an AWS key to GitHub, and just 10 minutes later got charged **$8,000** because an attacker used that key to spin up EC2 instances for crypto mining across every region simultaneously.

The scariest part wasn't the $8,000. It was that the key belonged to **root**. Nothing about it could be restricted — the attacker had exactly as much power as the actual account owner.

After reading that, I looked at my own project and realized I was sitting in the exact same risky situation — it just hadn't been "triggered" yet.

## 2. Why is the root account so dangerous?

The root account in AWS isn't like a regular IAM user. It has characteristics that multiply the risk many times over:

- **Permissions can't be restricted**: you can't attach a policy to limit root — it always has full access to every service, in every region.
- **There's no concept of "just enough access"**: an IAM Role can be scoped to read from one specific S3 bucket, but root has no such concept.
- **Root's access key is a long-lived credential**: once leaked, it stays valid until you manually go in and delete it — there's no automatic expiration.
- **The blast radius when leaked is total**: an attacker can create new users, delete CloudTrail logs, change billing alerts, or even shut down your entire account.

In other words, using root for everyday work is like carrying the building's master key everywhere you go — one that opens every single door — just to go grab a glass of water.

## 3. Three changes I made right after

After reading that case, I completely changed how I work with IAM. The three principles below have become non-negotiable habits in every project I work on now.

### ① Lock root away, don't use it daily

Root should really only be used for two things: creating the account for the first time, and enabling MFA (Multi-Factor Authentication). Once those two are done, "put root away" — don't use it to log into the console daily, don't use it to create resources.

Everything else — creating buckets, launching EC2, calling Bedrock — should go through an **IAM user** (when a human needs to act directly) or an **IAM Role** (when it's an application or service calling another).

### ② Never use an Access Key if an IAM Role will do

This was the most important distinction I hadn't fully understood before:

- An **Access Key** is a long-lived credential — a key/secret pair that exists forever until you actively delete or rotate it. If it's accidentally committed to GitHub (like in the case above), it stays "live" out there until you notice.
- An **IAM Role** works completely differently by design: the credentials it issues are temporary, they automatically expire after a set window, and AWS rotates them for you. There's no fixed secret sitting out there waiting to leak.

In my internship project — **AI Marketing Voice** — the FastAPI backend calls Bedrock, S3, and SES entirely through IAM Roles, scoped to exactly the 4 permission groups the app actually needs, nothing more. So if something ever did leak, an attacker could only do exactly what that role allows — no lateral movement into other services in the account.

### ③ Principle of Least Privilege — start from zero, add as needed

A very common (and very dangerous) habit is attaching broad policies like `AmazonS3FullAccess` or `AdministratorAccess` for convenience, just to avoid thinking too hard about it. The right approach is the opposite: **start from zero permissions, then add exactly what's needed, one permission at a time**.

For example, instead of granting access to every S3 bucket, specify explicitly:

- Which **bucket** is accessible (via a specific ARN).
- Which **actions** are allowed (`s3:GetObject`, `s3:PutObject`... instead of `s3:*`).

This might cost you an extra 5–10 minutes setting up a detailed policy up front, but it buys you a lot of peace of mind later — especially once your application goes to production and multiple people are maintaining it.

## 4. A quick checklist for anyone learning AWS

If you're at the stage of learning AWS that I was at before, here's a checklist I'd recommend doing right away — no need to wait until "the project gets bigger":

-  **Enable MFA on root today** — it only takes about 3 minutes, but significantly reduces the risk if credentials ever leak.
-  **Never commit AWS keys to Git** — always store credentials in a `.env` file, and make sure to add `.env` to `.gitignore` before your very first commit.
-  **Use IAM Roles for EC2, Lambda, and backend applications** — instead of hardcoding Access Keys into code or environment variables.
- **Scope resource ARNs specifically** — avoid leaving `"Resource": "*"` in a policy unless it's genuinely necessary.

## 5. Closing thoughts

$8,000 in 10 minutes is a steep price to pay for the "convenience" of sharing one root account for everything. What's worth noting is that this story isn't rare at all — it happens to a lot of people just starting out with cloud, simply because IAM is one of the most commonly overlooked pieces when getting started, even though it's the single most important security foundation of the entire system.

If there's one thing you take away from this post, let it be this: **root is only for unlocking the door the first time — let IAM Roles handle the rest for you.**

**Original Post (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/?multi_permalinks=2211124439652516&notif_id=1784348101969974&notif_t=feedback_reaction_generic&ref=notif