---
title : "Kiểm tra CloudWatch Logs & CloudFront"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

Sau khi đã gọi thử các API ở mục [5.4.3](../5.4.3-test-endpoint), bước này giúp bạn xác nhận log và audio
thật sự đã đi đúng qua CloudWatch Logs và CloudFront/S3.

#### Bước 1 — Kiểm tra CloudWatch Logs

1. Mở **Amazon CloudWatch console** → **Log groups** → chọn log group bạn đã đặt (ví dụ
   `/ai-marketing-voice/backend`).
2. Mở log stream mới nhất (mặc định tên `backend`, theo `CLOUDWATCH_LOG_STREAM`).
3. Xác nhận thấy các dòng log tương ứng với các hành động bạn vừa test, ví dụ:
   ```
   Đăng ký tài khoản mới: test@example.com
   User test@example.com tạo kịch bản: Chai cà phê cold-brew (-50 credit)
   User test@example.com tạo audio giọng Xuân Vĩnh (-25 credit)
   ```



#### Bước 2 — Kiểm tra file audio trên S3

1. Mở **S3 console** → vào bucket của bạn → prefix `audio/`.
2. Xác nhận có object `.wav` mới tương ứng với lần gọi `generate-voice` ở bước trước.

![object trong S3](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/17.png)

#### Bước 3 — Xác nhận audio phát được qua CloudFront

Nếu đã cấu hình `CLOUDFRONT_DOMAIN`, `audio_url` trả về từ API sẽ có dạng
`https://dxxxxxxxxxxxxx.cloudfront.net/audio/<file>.wav` thay vì presigned URL của S3. Mở link này trực tiếp
trên trình duyệt để xác nhận phát được, đồng thời xác nhận **không thể** truy cập trực tiếp file đó qua URL
S3 gốc (do bucket đã chặn public access, chỉ CloudFront với OAC mới đọc được).



#### Kiểm tra lại
- [ ] Log xuất hiện đầy đủ trên CloudWatch cho từng hành động (đăng ký, tạo kịch bản, tạo audio)
- [ ] File `.wav` xuất hiện đúng trong S3 bucket, prefix `audio/`
- [ ] `audio_url` trả về là domain CloudFront (không phải presigned S3 URL) và phát được bình thường
- [ ] Truy cập trực tiếp URL S3 gốc (không qua CloudFront) bị từ chối truy cập (do Block Public Access)
