---
title: "Published Blog Posts"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

###  [Blog 1- A Costly Lesson in IAM: From Root Account to Least Privilege](3.1-Blog1/)
This blog recounts an expensive lesson from early AWS days: after accidentally committing a root account's AWS key to GitHub, a user got charged $8,000 in just 10 minutes as an attacker used that key to spin up EC2 instances for crypto mining across every region. From that story, the post shares three key changes in how to work with IAM: locking root away and using it only to enable MFA, favoring IAM Roles over Access Keys since their credentials automatically expire and rotate, and applying the Principle of Least Privilege — starting from zero permissions and adding only what's needed, instead of attaching full-access policies for convenience.

###  [Blog 2 - An Overview of Amazon Bedrock AgentCore Payments](3.2-Blog2/)
This blog introduces an AI Agent that can pull out its own wallet to pay for data, call APIs, or even hire another Agent to do work on its behalf — with no human needed to click a confirmation button. That's exactly what AWS just introduced with Amazon Bedrock AgentCore Payments. As more and more services move to pay-per-call pricing costing just fractions of a cent, letting an Agent manage its own wallet and pay autonomously within an authorized budget is no longer a distant vision — it's now a real feature, built on x402, an open protocol developed by Coinbase.

###  [Blog 3 - AWS Lambda MicroVMs — When Serverless Gets VM-Level Power](3.3-Blog3/)
This blog introduces AWS Lambda MicroVMs — an entirely new serverless compute primitive that provides VM-level isolated sandboxes, with no shared kernel or resources between sessions. While traditional Lambda forces a tradeoff between speed, cost, and the level of isolation or state retention you get — pushing many teams to grudgingly move to containers or EC2 just for deeper control — Lambda MicroVMs, built on Firecracker, fills exactly that gap: near-instant startup, state preservation for up to 8 hours, full lifecycle control, all without managing any infrastructure yourself.