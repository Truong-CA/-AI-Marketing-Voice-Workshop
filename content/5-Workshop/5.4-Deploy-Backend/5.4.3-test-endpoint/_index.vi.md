---
title : "Kiểm thử API tạo kịch bản & giọng nói"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

#### Bước 1 — Đăng ký tài khoản test

```
curl -X POST http://localhost:8000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name": "Test User", "email": "test@example.com", "password": "secret123"}'
```

Ghi lại `id` trả về — đây là giá trị bạn sẽ dùng cho header `X-User-Id` ở các bước sau hệ thống xác thực đơn
giản, không dùng JWT — xem thêm ở `core/security.py`.


Nếu đã cấu hình `SES_SENDER_EMAIL` và verify đúng email nhận, kiểm tra hộp thư để xác nhận email chào mừng đã
được gửi thành công qua SES.

#### Bước 2 — Gọi API tạo kịch bản

```
curl -X POST http://localhost:8000/api/v1/generate-script \
  -H "X-User-Id: <id-vừa-đăng-ký>" \
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


Kiểm tra phản hồi có đủ `title`, `hook`, `body`, `cta`, `hashtags`, `full_script`, và `credits_remaining` đã
giảm đúng 50 so với số dư ban đầu (200 credit tặng khi đăng ký).

#### Bước 3 — Gọi API tạo giọng đọc

```
curl -X POST http://localhost:8000/api/v1/generate-voice \
  -H "X-User-Id: <id-vừa-đăng-ký>" \
  -H "Content-Type: application/json" \
  -d '{"script": "<full_script lấy từ bước trên>", "voice_id": "Xuân Vĩnh"}'
```



Mở `audio_url` trả về bằng trình duyệt hoặc `curl -O` để tải xuống và nghe thử, xác nhận đúng nội dung kịch
bản vừa tạo.

#### Bước 4 — Kiểm thử xử lý lỗi
Thử gọi lại `generate-script` với `X-User-Id` không tồn tại hoặc thiếu `product_name` — xác nhận backend trả
lỗi rõ ràng (`401` hoặc `400`) thay vì crash.

#### Kiểm tra lại
- [ ] Đăng ký thành công, credit ban đầu = 200
- [ ] `generate-script` trả kịch bản đúng cấu trúc, trừ đúng 50 credit
- [ ] `generate-voice` trả về `audio_url` nghe được, trừ credit theo độ dài script
- [ ] Gọi thiếu `X-User-Id` trả lỗi `401` thay vì crash server
