---
title : "Triển khai frontend & kiểm thử end-to-end"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Bước 1 — Cấu hình frontend

Cấu trúc dự án: 
```text
AI Marketing Voice
│
└── frontend
    ├── .env
    ├── .gitignore
    ├── README.md
    ├── package.json
    ├── package-lock.json
    ├── public
    │   ├── favicon.ico
    │   ├── index.html
    │   ├── logo192.png
    │   ├── logo512.png
    │   ├── manifest.json
    │   └── robots.txt
    │
    └── src
        ├── App.js
        ├── App.css
        ├── App.test.js
        ├── index.js
        ├── index.css
        ├── logo.svg
        ├── reportWebVitals.js
        └── setupTests.js
```
Trong file `frontend/.env`, trỏ ứng dụng về backend đang chạy:

```
REACT_APP_API_BASE=http://localhost:8000/api/v1
REACT_APP_GOOGLE_CLIENT_ID=<google oauth client id của bạn>
```

#### Bước 2 — Cài đặt và chạy frontend

```
cd frontend
npm install
npm start
```

Mở `http://localhost:3000`.

![frontend đang chạy](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/19.png)

#### Bước 3 — Chạy luồng end-to-end

![quy trinh end to end](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/ete.png)

1. **Đăng ký/Đăng nhập** trên ứng dụng (email/mật khẩu hoặc Google Sign-In) — xác nhận credit chào mừng
   (200 credit) hiển thị đúng ở sidebar, và kiểm tra hộp thư đã nhận được email chào mừng qua SES.
2. Sang tab **Chọn giọng nói**, bấm ▶ để nghe thử vài giọng đọc tiếng Việt khác nhau , lần đầu mỗi giọng sẽ
   mất vài giây để tạo mẫu, các lần sau nhanh hơn vì đã cache.
3. Quay lại tab **Tạo kịch bản**, điền form thông tin sản phẩm và nhấn **Tạo kịch bản ngay** — xác nhận kịch
   bản (hook/body/CTA/hashtag) trả về và credit bị trừ đúng 50.
4. Nhấn **Tạo audio** trên kịch bản vừa tạo (đã chọn giọng ở bước 2) — xác nhận có trình phát/link tải audio.
5. Phát thử file audio để xác nhận đúng nội dung tiếng Việt khớp với kịch bản.
6. Vào tab **Lịch sử credit** — xác nhận các giao dịch tặng credit đăng ký, trừ credit tạo kịch bản, trừ
   credit tạo audio hiển thị đầy đủ và đúng thứ tự thời gian.

<video width="100%" controls>
    <source src="/Images/demo.mp4" type="video/mp4">
    Trình duyệt của bạn không hỗ trợ video.
</video>

#### Bước 4 — Kiểm tra phía AWS

- **S3 console** → vào bucket của bạn → prefix `audio/` — xác nhận object `.wav` mới đã được tạo.
- **Amazon CloudFront** → mở `audio_url` trả về, xác nhận domain là CloudFront chứ không phải S3 trực tiếp.
- **Amazon Bedrock console** → kiểm tra log/metric lượt gọi model Claude 3 Sonnet (nếu có bật).
- **Amazon SES console** → **Sending statistics** — xác nhận có lượt gửi thành công tương ứng với email chào
  mừng vừa nhận.
- **Amazon CloudWatch Logs** → xác nhận log group nhận đủ các dòng log tương ứng với thao tác bạn vừa làm
  trên frontend (đăng ký, tạo kịch bản, tạo audio).
- **IAM console** → xác nhận backend vẫn chỉ dùng đúng policy least-privilege đã tạo ở bước trước (không cần
  thêm quyền nào khác).



#### Bước 5 — Kiểm thử xử lý lỗi
Thử nhập dữ liệu không hợp lệ (ví dụ để trống tên sản phẩm, hoặc chọn platform không nằm trong
TikTok/Instagram/Facebook/YouTube) và xác nhận backend trả về lỗi `400` rõ ràng thay vì bị crash — điều này
kiểm chứng logic trong `prompt/validator.py`.
