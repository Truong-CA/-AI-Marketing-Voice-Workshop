---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AI Marketing Voice On AWS: AI-Powered Marketing Script & Voice Generation Platform on AWS

#### Overview

**AI Marketing Voice** is a project I independently built, using **Amazon Bedrock** to
generate marketing scripts for short videos, **VieNeu-TTS** to convert those scripts into voiceovers, with audio files stored on **Amazon S3** and distributed via **Amazon CloudFront**. The system also sends notification emails via **Amazon SES** and records operational logs via **Amazon CloudWatch Logs**. In this workshop, I will set up the necessary AWS resources and permissions, deploy a FastAPI backend connected to the above services, connect the React frontend, run end-to-end tests, and clean up all resources upon completion.

This workshop is a product I independently implemented for this internship — it is **not** a copy of any existing workshop.

The AWS services we need to prepare:

- **Amazon Bedrock** — provides access to high-quality foundation models (Claude 3 Sonnet) through a fully managed API, with no need to self-host or fine-tune an LLM.
- **Amazon S3** — durable, low-cost object storage for generated audio files.
- **Amazon CloudFront** — CDN in front of S3, enabling faster audio playback for users across multiple geographic locations and reducing the number of direct access requests to the bucket.
- **Amazon SES** — reliable transactional email delivery with a generous free tier, with no need to operate a mail server.
- **Amazon CloudWatch Logs** — centralizes application logs in one place, making it easy to look up errors and activity instead of relying on scattered console logs across individual servers.

- **VieNeu-TTS** — an open-source Vietnamese TTS model running on the backend's own CPU, free of charge and independent of any external service for the voiceover component.

![End-to-End Architecture](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/ete.png)

![Script Generation Flow](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/kb.png)

![Audio Generation Flow](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/qtat.png)

![Credit Flow](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/credit.png)


#### Contents

1. [Workshop Overview](5.1-Workshop-overview)
2. [Prerequisites](5.2-Prerequiste/)
3. [AWS Resources & IAM Setup](5.3-Setup-AWS-Resources/)
4. [Backend Deployment (Bedrock + VieNeu-TTS + S3/CloudFront + SES + CloudWatch Integration)](5.4-Deploy-Backend/)
5. [Frontend Deployment & End-to-End Testing](5.5-Deploy-Frontend-Test/)
6. [Resource Cleanup](5.6-Cleanup/)