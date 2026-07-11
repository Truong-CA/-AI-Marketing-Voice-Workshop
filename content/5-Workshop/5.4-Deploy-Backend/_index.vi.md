---
title : "Triển khai backend"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Bước 1 — Cấu hình biến môi trường

Tao file `backend/.env`, điền các tài nguyên đã tạo ở bước trước:

```
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=<access key>
AWS_SECRET_ACCESS_KEY=<secret key>

S3_BUCKET_NAME=ai-marketing-voice
CLOUDFRONT_DOMAIN=<domain distribution của bạn>.cloudfront.net

SES_SENDER_EMAIL=<email đã verify>

CLOUDWATCH_LOG_GROUP=/ai-marketing-voice/backend
CLOUDWATCH_LOG_STREAM=backend

GOOGLE_CLIENT_ID=<google oauth client id>
DATABASE_URL=sqlite:///./app.db
```

Xem chi tiết từng bước tại các mục con: [5.4.1 Chuẩn bị môi trường](5.4.1-prepare),
[5.4.2 Kiểm tra VieNeu-TTS](5.4.2-create-interface-enpoint),
[5.4.3 Kiểm thử API](5.4.3-test-endpoint),
[5.4.4 Kiểm tra CloudWatch & CloudFront](5.4.4-dns-simulation).

Cấu trúc dự án: 

```text
AI Marketing Voice
├── backend
│   ├── .env
│   ├── app.db
│   ├── database.py
│   ├── main.py
│   ├── requirements.txt
│   ├── test_bedrock.py
│   ├── generated_audio/
│   │
│   ├── api
│   │   ├── auth.py
│   │   ├── credits.py
│   │   ├── generate.py
│   │   └── voice.py
│   │
│   ├── core
│   │   ├── __init__.py
│   │   ├── logging_config.py
│   │   └── security.py
│   │
│   ├── models
│   │   ├── db_models.py
│   │   └── schemas.py
│   │
│   ├── prompt
│   │   ├── prompt_builder.py
│   │   └── validator.py
│   │
│   └── services
│       ├── bedrock_service.py
│       ├── s3_service.py
│       ├── ses_service.py
│       └── vieneu_service.py
│

```

#### Bước 2 — Cài đặt dependencies và chạy backend local

```
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

Mở `http://localhost:8000` — bạn sẽ thấy:

![Backend đang chạy](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/13.png)

#### Bước 3 — Backend giao tiếp với Bedrock và VieNeu-TTS như thế nào

Endpoint **tạo kịch bản** (`POST /api/v1/generate-script`) xây dựng prompt từ request (`prompt_builder.py`),
sau đó gọi `BedrockService.generate_script()`:

```python
response = self.client.invoke_model(
    modelId=self.model_id,          # anthropic.claude-3-sonnet-20240229-v1:0
    body=json.dumps(body),
    contentType="application/json",
    accept="application/json",
)
result = json.loads(response["body"].read())
raw_text = result["content"][0]["text"]
parsed = json.loads(raw_text)       # model được yêu cầu trả về đúng định dạng JSON
```

Endpoint **tạo giọng đọc** (`POST /api/v1/generate-voice`) gọi `VieNeuService.generate_voice()` — tổng hợp
giọng nói tiếng Việt bằng model chạy local, rồi upload lên S3 và trả về URL qua CloudFront:

```python
tts = self._get_tts()                       # model duoc lazy-load 1 lan cho ca process
wav = tts.infer(text=text, voice=voice_id)  # vd voice_id = "Xuân Vĩnh"
tts.save(wav, tmp_path)

# Upload len S3, phuc vu qua CloudFront (hoac fallback presigned S3 URL neu chua co CLOUDFRONT_DOMAIN)
self.s3.put_object(Bucket=self.bucket, Key=key, Body=data, ContentType="audio/wav")
url = f"https://{cloudfront_domain}/{key}"
```

Song song đó, `SESService.send_email()` gửi email khi có tài khoản mới đăng ký, và mọi
bước quan trọng đều được ghi log qua `core/logging_config.py` console + CloudWatch Logs nếu đã cấu hình.

Cả hai endpoint tạo kịch bản/giọng đọc đều yêu cầu người dùng đã đăng nhập và sẽ trừ credit từ số dư của
người dùng đó, đồng thời ghi lại thành một `CreditTransaction` để dễ kiểm tra sau này.

#### Bước 4 — Kiểm thử API trực tiếp

Đăng ký một tài khoản, sau đó gọi endpoint tạo kịch bản (thay `<USER_ID>` bằng `id` trả về từ
`/api/v1/auth/register` hoặc `/api/v1/auth/login`):

```
curl -X POST http://localhost:8000/api/v1/generate-script \
  -H "X-User-Id: <USER_ID>" \
  -H "Content-Type: application/json" \
  -d '{
        "product_name": "Chai cà phê cold-brew",
        "description": "Cà phê cold brew uống liền, chai thủy tinh 350ml",
        "price": "45.000 VNĐ",
        "audience": "Dân văn phòng, 22-35 tuổi",
        "platform": "TikTok",
        "tone": "Vui vẻ",
        "duration": "30s",
        "cta": "Mua ngay"
      }'
```

Bạn sẽ nhận được kịch bản dạng JSON. Sau đó đưa `full_script` trả về vào endpoint `/api/v1/generate-voice`
cùng `voice_id` (lấy danh sách từ `GET /api/v1/voices`) để nhận về URL audio qua CloudFront 

