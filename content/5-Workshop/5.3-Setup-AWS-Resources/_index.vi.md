---
title : "Thiết lập tài nguyên AWS & IAM"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Bước 1 — Tạo S3 bucket lưu file audio

1. Vào **S3 console** → **Create bucket**.
2. Đặt tên duy nhất, ví dụ `ai-marketing-voice`.
3. Bật **Block all public access** (bucket sẽ được truy cập qua CloudFront, không cần public trực tiếp).
4. Nhấn **Create bucket**.

![tạo bucket](/images/3.png)

*(Bước tạo CloudFront distribution trỏ về bucket này được hướng dẫn chi tiết ở mục [5.3.1](5.3.1-create-gwe),
và cấu hình SES + CloudWatch Logs ở mục [5.3.2](5.3.2-test-gwe).)*

#### Bước 2 — Bật quyền truy cập model trong Amazon Bedrock

1. Vào **Amazon Bedrock console** → **Model access**.
2. Yêu cầu/bật quyền truy cập cho **Anthropic Claude 3 Sonnet**.
3. Chờ đến khi trạng thái hiển thị **Access granted**.

![bedrock model access](/images/1.png)

#### Bước 3 — Tạo IAM policy least-privilege

Tạo một policy chỉ cho phép đúng những gì backend cần — gọi model Claude 3 Sonnet, đọc/ghi object trong bucket
của bạn, gửi email qua SES, và ghi log lên CloudWatch:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "InvokeBedrockModel",
      "Effect": "Allow",
      "Action": "bedrock:InvokeModel",
      "Resource": "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0"
    },
    {
      "Sid": "S3AudioBucketAccess",
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:GetObject"],
      "Resource": "arn:aws:s3:::ai-marketing-voice/*"
    },
    {
      "Sid": "SESSendWelcomeEmail",
      "Effect": "Allow",
      "Action": ["ses:SendEmail", "ses:SendRawEmail"],
      "Resource": "*"
    },
    {
      "Sid": "CloudWatchLogsWrite",
      "Effect": "Allow",
      "Action": ["logs:CreateLogGroup", "logs:CreateLogStream", "logs:PutLogEvents"],
      "Resource": "arn:aws:logs:us-east-1:*:log-group:/ai-marketing-voice/*"
    }
  ]
}
```


#### Bước 4 — Tạo IAM user hoặc role và gắn policy

1. Vào **IAM console** → **Users** → **Create user** (ví dụ `ai-marketing-voice-backend`).
2. Gắn trực tiếp policy vừa tạo ở Bước 3 cho user này (least privilege — không gắn thêm managed policy rộng
   hơn mức cần thiết).
3. Tạo **access key** cho user này. Khi triển khai thật, nên ưu tiên dùng **IAM role**
   gắn vào tài nguyên compute thay vì access key tồn tại lâu dài.


#### Kiểm tra lại
Xác nhận đã có:
- [ ] Tên S3 bucket đã ghi lại
- [ ] Bedrock model access hiển thị "Access granted" cho Claude 3 Sonnet
- [ ] IAM policy chỉ giới hạn đúng 4 nhóm quyền ở trên (Bedrock, S3, SES, CloudWatch Logs)
- [ ] Access key / role sẵn sàng dùng trong file `.env` của backend
- [ ] Đã hoàn thành [5.3.1 - Tạo CloudFront Distribution](5.3.1-create-gwe) và
      [5.3.2 - Cấu hình SES & CloudWatch Logs](5.3.2-test-gwe)
