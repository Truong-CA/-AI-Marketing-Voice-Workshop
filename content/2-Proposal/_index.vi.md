---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AI Marketing Voice — Nền tảng tạo kịch bản & giọng đọc marketing bằng AI trên AWS

### 1. Tóm tắt dự án
**AI Marketing Voice** là một ứng dụng web giúp các doanh nghiệp nhỏ và người sáng tạo nội dung nhanh chóng
tạo ra kịch bản marketing cho video ngắn và giọng đọc tiếng Việt sẵn sàng sử dụng cho các nền tảng
mạng xã hội (TikTok, Instagram, Facebook, YouTube). Người dùng chỉ cần mô tả sản phẩm; hệ thống sử dụng
**Amazon Bedrock** với mô hình Claude 3 Sonnet để tạo kịch bản có cấu trúc với những hook, nội dung, CTA, hashtag kèm sự kết hợp với
**VieNeu-TTS** là mô hình text-to-speech tiếng Việt mã nguồn mở, chạy ngay trên server backend để tổng hợp
giọng đọc, sau đó lưu vào **Amazon S3** và phân phối qua **Amazon CloudFront**. Nền tảng có hệ thống tài
khoản (email/mật khẩu + đăng nhập Google), gửi email thông báo qua **Amazon SES**, ghi log vận hành qua
**Amazon CloudWatch Logs**, và cơ chế tính phí theo credit để kiểm soát chi phí công bằng cho từng người dùng.

### 2. Vấn đề cần giải quyết
**Vấn đề là gì?**
Các doanh nghiệp nhỏ và người sáng tạo nội dung cá nhân thường không có đủ thời gian, kỹ năng viết quảng cáo,
hoặc ngân sách để viết kịch bản phù hợp từng nền tảng và thu âm giọng đọc tiếng Việt chuyên nghiệp cho mỗi sản
phẩm muốn quảng bá. Thuê copywriter hoặc diễn viên lồng tiếng cho từng video ngắn vừa chậm vừa tốn kém; các
dịch vụ TTS đám mây phổ biến lại chưa hỗ trợ tốt giọng đọc tiếng Việt tự nhiên.

**Giải pháp**
AI Marketing Voice cho phép người dùng nhập các thông tin cơ bản về sản phẩm (tên, mô tả, giá, đối tượng mục
tiêu, nền tảng, giọng điệu, thời lượng, lời kêu gọi hành động - CTA) và nhận ngay: 
- Kịch bản được tạo bởi mô hình ngôn ngữ lớn.
- File audio giọng đọc **tiếng Việt** của kịch bản đó, chọn từ 10 giọng đọc đa dạng (nam/nữ, giọng Bắc/Nam, phong cách tự nhiên/kể chuyện/tin tức).
  
Tất cả chỉ trong một luồng
request-response duy nhất, với chi phí và thời gian thấp hơn rất nhiều so với sản xuất thủ công.

### 3. Kiến trúc giải pháp

**Các dịch vụ AWS sử dụng:**
- **Amazon Bedrock** — gọi mô hình nền tảng Claude 3 Sonnet để tạo kịch bản marketing dưới dạng JSON có cấu trúc.
- **Amazon S3** — lưu trữ file audio đã tạo (từ VieNeu-TTS) và làm origin cho CloudFront.
- **Amazon CloudFront** — CDN đứng trước S3, phân phối file audio nhanh hơn và giảm tải truy cập trực tiếp
  vào bucket.
- **Amazon SES** — gửi email thông báo tự động khi người dùng đăng ký tài khoản mới
- **Amazon CloudWatch Logs** — thu thập log ứng dụng backend (đăng ký, đăng nhập, tạo kịch bản, tạo audio,
  lỗi hệ thống) để theo dõi và debug khi vận hành thật.
- **AWS IAM** — quyền hạn least-privilege cho backend để gọi Bedrock, S3, SES và CloudWatch Logs.

**Giọng đọc:** hệ thống dùng **VieNeu-TTS**, một mô hình text-to-speech tiếng Việt
mã nguồn mở có giấy phép Apache 2.0, phù hợp vì các dịch vụ TTS thương mại.

![Kiến trúc hệ thống](/images/main.png)

**Thiết kế thành phần:**
- **Frontend** — thu thập thông tin sản phẩm từ người dùng, gọi API backend, chọn giọng đọc, phát/tải
  audio đã tạo.
- **Backend** — kiểm tra dữ liệu đầu vào, xây dựng prompt cho LLM, gọi Bedrock, gọi VieNeu-TTS tổng
  hợp giọng nói, upload audio lên S3 phục vụ qua CloudFront, gửi email chào mừng qua SES, ghi log qua
  CloudWatch Logs, quản lý tài khoản người dùng và sổ ghi credit.
- **Tầng prompt** — một prompt builder riêng sẽ ánh xạ nền tảng (TikTok/Instagram/Facebook/YouTube), giọng điệu
  và thời lượng thành một prompt có cấu trúc, cùng với validator kiểm tra các trường bắt buộc trước khi gọi Bedrock.
- **Hệ thống credit** — mỗi lượt đăng ký được tặng credit chào mừng; mỗi lần tạo kịch bản và tạo giọng đọc sẽ
  trừ credit tương ứng, mọi giao dịch đều được ghi lại để dễ kiểm tra.

![](C:/Users/Loc/Desktop/workshop/WorkShop_Final/static/images/ete.png)

![Quy trình tạo script](/images/kb.png)

![Quy trình tạo Am Thanh](/images/qtat.png)

![Luồng Credit](/images/credit.png)

### 4. Triển khai kỹ thuật
**Các giai đoạn triển khai**
- Thiết kế API REST và mô hình dữ liệu  — Tuần 1–2.
- Xây dựng prompt builder + validator cho việc tạo kịch bản — Tuần 3.
- Tích hợp Amazon Bedrock để tạo kịch bản và kiểm thử với sản phẩm mẫu — Tuần 4.
- Tích hợp VieNeu-TTS + Amazon S3 + CloudFront để tạo và phân phối audio — Tuần 5.
- Thêm xác thực, hệ thống credit, và email thông báo qua SES — Tuần 6–7.
- Xây dựng và kết nối frontend React với API — Tuần 8–9.
- Thêm CloudWatch Logs, kiểm thử, siết chặt IAM, và quy trình dọn dẹp chi phí — Tuần 10–12.

**Yêu cầu kỹ thuật**
- Tài khoản AWS đã được cấp quyền truy cập mô hình Claude 3 Sonnet trong Amazon Bedrock.
- Một S3 bucket để lưu trữ audio đã tạo, và một CloudFront distribution trỏ về bucket đó.
- Một địa chỉ email đã verify trong Amazon SES để gửi email chào mừng.
- Một CloudWatch Log Group để nhận log từ backend.
- Một IAM role/user với quyền tối thiểu cho `bedrock:InvokeModel`, `s3:PutObject`/`s3:GetObject` giới hạn
  trong bucket mục tiêu, `ses:SendEmail`, và `logs:CreateLogGroup`/`logs:CreateLogStream`/`logs:PutLogEvents`.
- Python 3.10+, FastAPI, boto3, SQLAlchemy, thư viện `vieneu` (TTS local); Node.js + React cho frontend.

### 5. Timeline & Mốc tiến độ
- **Tháng 1:** Học kiến thức nền tảng AWS, IAM, thực hành riêng lẻ với Bedrock, S3, SES, CloudWatch.
- **Tháng 2:** Xây dựng và tích hợp backend (tạo kịch bản + giọng đọc local, xác thực, hệ thống credit, logging).
- **Tháng 3:** Xây dựng frontend, kết nối end-to-end, kiểm thử, tối ưu chi phí/bảo mật và viết báo cáo này.

### 6. Ước tính ngân sách

Chi phí hạ tầng: 300 lượt tạo kịch bản/tháng, 300 lượt tạo audio/tháng

Amazon Bedrock (Claude 3 Sonnet): 2,97 USD/tháng (300 lượt gọi model, trung bình 800 input + 500 output token/lượt).
Amazon S3 Standard: 0,01 USD/tháng (0,3 GB lưu trữ audio, 300 PUT request).
Amazon CloudFront: 0,05 USD/tháng (0,6 GB truyền dữ liệu ra, 600 request phát/tải).
Amazon SES: 0,00 USD/tháng (trong hạn mức miễn phí 3.000 email/tháng cho 12 tháng đầu).
Amazon CloudWatch Logs: 0,00 USD/tháng (trong hạn mức miễn phí 5 GB log/tháng).
AWS IAM: 0,00 USD/tháng (miễn phí).
Tổng: 3,03 USD/tháng, 36,36 USD/12 tháng



### 7. Đánh giá rủi ro
- **Model trả về không đúng định dạng JSON** — giảm thiểu bằng cách ràng buộc chặt chẽ định dạng trong prompt
  và validate ở backend, trả lỗi rõ ràng.
- **Phát sinh chi phí do sử dụng không giới hạn (Bedrock)** — giảm thiểu bằng hệ thống credit, giới hạn mức
  dùng theo từng người dùng.
- **Lộ AWS credentials** — giảm thiểu bằng cách dùng biến môi trường và áp dụng
  IAM least-privilege.
- **VieNeu-TTS chạy CPU chậm hơn dịch vụ TTS đám mây** — chấp nhận đánh đổi tốc độ để đổi lấy chi phí bằng 0 và
  không phụ thuộc bên thứ ba; có cache cho các mẫu giọng nghe thử để giảm số lần phải chạy lại mô hình.
- **SES ở chế độ Sandbox chỉ gửi được tới email đã verify** — cần xin cấp quyền Production access trước khi
  phát hành rộng rãi cho người dùng thật.

### 8. Kết quả mong đợi
Xây dựng thành công một hệ thống kha hoàn chỉnh, cho phép người dùng nhập thông tin sản phẩm, tự động tạo kịch bản marketing bằng Amazon Bedrock, chuyển kịch bản thành giọng đọc tiếng Việt với 10 lựa chọn giọng đọc bằng VieNeu-TTS và tải file âm thanh về thông qua CloudFront. Hệ thống hoạt động ổn định, bảo mật theo nguyên tắc least privilege, ghi log đầy đủ bằng CloudWatch và có thể dễ dàng dọn dẹp toàn bộ tài nguyên AWS sau khi hoàn thành