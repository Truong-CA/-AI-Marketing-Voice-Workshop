---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AI Marketing Voice On AWS: nền tảng tạo kịch bản & giọng đọc marketing bằng AI trên AWS

#### Tổng quan

**AI Marketing Voice** là dự án do tôi tự xây dựng, sử dụng **Amazon Bedrock** để
tạo kịch bản marketing cho video ngắn, **VieNeu-TTS** để chuyển kịch bản đó thành giọng đọc, với file audio được lưu trên **Amazon S3** và phân
phối qua **Amazon CloudFront**. Hệ thống còn gửi email thông báo qua **Amazon SES** và ghi log vận hành qua
**Amazon CloudWatch Logs**. Trong workshop này, toi sẽ thiết lập các tài nguyên và quyền AWS cần thiết, triển
khai backend FastAPI kết nối với các dịch vụ trên, kết nối frontend React, chạy kiểm thử end-to-end, và dọn
dẹp toàn bộ tài nguyên sau khi hoàn tất.

Workshop này là sản phẩm tôi tự triển khai cho kỳ thực tập này — **không** phải bản sao của bất cứ workshop nào.

Các dịch vụ của AWS mà chúng ta cần phải chuẩn bị: 

- **Amazon Bedrock** — cho phép truy cập mô hình nền tảng chất lượng cao (Claude 3 Sonnet) qua một API được
  quản lý hoàn toàn, không cần tự host hay fine-tune LLM.
- **Amazon S3** — lưu trữ object bền vững, chi phí thấp cho file audio đã tạo.
- **Amazon CloudFront** — CDN đứng trước S3, giúp phát audio nhanh hơn cho người dùng ở nhiều vị trí địa lý và
  giảm số lượt truy cập trực tiếp vào bucket.
- **Amazon SES** — gửi email giao dịch (transactional email) đáng tin cậy, có mức miễn phí hào phóng, không
  cần tự vận hành mail server.
- **Amazon CloudWatch Logs** — tập trung log ứng dụng ở một nơi, dễ tra cứu lỗi/hoạt động thay vì chỉ có log
  console rời rạc trên từng máy chủ.

- **Amazon CloudWatch Logs** — tập trung log ứng dụng ở một nơi, dễ tra cứu lỗi/hoạt động thay vì chỉ có log.
  
- **VieNeu-TTS** — một mô hình TTS tiếng Việt mã nguồn mở, chạy trên CPU của chính backend, miễn phí và không phụ thuộc dịch vụ ngoài cho phần giọng đọc.

![Kiến trúc End To End](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/ete.png) 

![Quy trình tạo script](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/kb.png)

![Quy trình tạo Am Thanh](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/qtat.png)

![Luồng Credit](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/credit.png)


#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview)
2. [Điều kiện tiên quyết](5.2-Prerequiste/)
3. [Thiết lập tài nguyên AWS & IAM](5.3-Setup-AWS-Resources/)
4. [Triển khai backend (tích hợp Bedrock + VieNeu-TTS + S3/CloudFront + SES + CloudWatch)](5.4-Deploy-Backend/)
5. [Triển khai frontend & kiểm thử end-to-end](5.5-Deploy-Frontend-Test/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)
