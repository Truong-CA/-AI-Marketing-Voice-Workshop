---
title : "Chuẩn bị môi trường"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

Trước khi chạy backend, hãy chuẩn bị môi trường Python riêng biệt để tránh xung đột thư viện.

#### Bước 1 — Tạo virtual environment

```
cd backend
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
```



#### Bước 2 — Cài dependencies

```
pip install -r requirements.txt
```

Chú ý các thư viện quan trọng trong `requirements.txt`:
- `boto3` — gọi Bedrock, S3, SES, CloudWatch Logs
- `vieneu` — model TTS tiếng Việt chạy local (CPU)
- `watchtower` — gửi log Python lên CloudWatch Logs
- `fastapi`, `uvicorn`, `sqlalchemy`, `bcrypt`, `google-auth` — API, database, xác thực



#### Bước 3 — Điền file `.env`

Tao `backend/.env` rồi điền đầy đủ theo hướng dẫn ở [mục 5.4](../) (AWS keys,
S3 bucket, CloudFront domain, SES sender email, CloudWatch log group, Google Client ID).

{{% notice tip %}}
Nếu bạn để trống `CLOUDFRONT_DOMAIN`, `SES_SENDER_EMAIL`, hoặc `CLOUDWATCH_LOG_GROUP`, backend vẫn khởi động
và chạy được bình thường — hệ thống tự fallback (audio phục vụ local/presigned S3 URL, bỏ qua gửi email, chỉ
log ra console) thay vì báo lỗi. Điều này giúp bạn có thể chạy thử từng phần một thay vì phải cấu hình đủ cả
4 dịch vụ mới bắt đầu được.
{{% /notice %}}
