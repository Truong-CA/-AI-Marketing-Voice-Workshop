---
title : "Giới thiệu"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### AI Marketing Voice là gì?
AI Marketing Voice là một ứng dụng web dành cho doanh nghiệp nhỏ và người sáng tạo nội dung. 

1. Người dùng điềnthông tin sản phẩm (tên, mô tả, giá, đối tượng mục tiêu, nền tảng, giọng điệu, thời lượng, CTA) 
2. Ung dụng sẽ trả về một kịch bản marketing có cấu trúc (tiêu đề, hook, nội dung, CTA, hashtag) được tạo bởi **Amazon Bedrock**.
3. Một file audio giọng đọc **tiếng Việt** của kịch bản đó nguười dung chọn 1 trong 10 giọng, được tạo bởi **VieNeu-TTS**
   chạy ngay trên server backend, lưu trên **Amazon S3** và phân phối qua **Amazon CloudFront**.

Ngoài ra, khi người dùng đăng ký tài khoản mới, hệ thống gửi email thông báo qua **Amazon SES**, và mọi hoạt
động quan trọng (đăng ký, đăng nhập, tạo kịch bản, tạo audio, lỗi hệ thống) được ghi lại qua **Amazon
CloudWatch Logs** để dễ theo dõi/debug.


#### Tổng Quan workshop

![sơ đồ tổng quan](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/main.png)

