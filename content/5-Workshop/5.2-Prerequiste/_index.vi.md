---
title : "Các bước chuẩn bị"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---


#### Thiết lập tài khoản và các dịch vụ của AWS
- Một tài khoản AWS đã bật thanh toán.

- **Bật quyền truy cập mô hình Claude 3 Sonnet** trong **Amazon Bedrock**, việc phê duyệt có thể mất vài phút.
- Chọn một Region AWS có hỗ trợ Bedrock (ví dụ `us-east-1`).
- Một địa chỉ email bạn có quyền truy cập, để verify trong **Amazon SES** (dùng làm địa chỉ gửi email chào mừng).

#### Công cụ cài đặt trên máy local
- Python 3.10+ và `pip`
- Node.js 18+ và `npm` 
- AWS CLI v2, đã cấu hình bằng `aws configure`
- Git
- Ít nhất ~2GB dung lượng trống và kết nối mạng ổn định để VieNeu-TTS tự tải model từ Hugging Face ở lần
  chạy đầu tiên.

#### Quyền AWS sẽ tạo ở bước tiếp theo
- Một IAM role/user với quyền tối thiểu cho:
  - `bedrock:InvokeModel` (giới hạn theo ARN của model Claude 3 Sonnet)
  - `s3:PutObject`, `s3:GetObject` (giới hạn chỉ trong S3 bucket của dự án)
  - `ses:SendEmail`, `ses:SendRawEmail`
  - `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`

#### Mã nguồn dự án
Clone/tải về repository dự án của bạn (backend + frontend) để có thể cấu hình và chạy local hoặc triển khai,
ví dụ:
```
git clone <https://github.com/Truong-CA/AI-ContentCreator>
cd "AI Marketing Voice"
```
![Github](/images/12.png)