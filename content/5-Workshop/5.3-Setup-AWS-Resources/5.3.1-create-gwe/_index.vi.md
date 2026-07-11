---
title : "Tạo CloudFront Distribution"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

Phần này hướng dẫn tạo một **Amazon CloudFront distribution** đứng trước S3 bucket đã tạo ở bước trước, để
phát file audio nhanh hơn thay vì phải dùng presigned URL trực tiếp từ S3.

#### Bước 1 — Cấu hình Origin Access Control (OAC)
1. Mở **Amazon CloudFront console** → **Origin access** → **Create control setting**.
2. Đặt tên, giữ **Sign requests** mặc định, **Create**.

Origin Access Control cho phép CloudFront đọc object trong S3 bucket dù bucket vẫn ở chế độ **Block all
public access** — người dùng chỉ có thể truy cập audio qua domain CloudFront, không thể truy cập thẳng vào S3.



#### Bước 2 — Tạo Distribution
1. Vào **Distributions** → **Create distribution**.
2. **Origin domain**: chọn S3 bucket đã tạo ở [5.3](../).
3. **Origin access control settings**: chọn OAC vừa tạo ở Bước 1.
4. **Viewer protocol policy**: chọn **Redirect HTTP to HTTPS**.
5. Giữ nguyên các mặc định khác, nhấn **Create distribution**.

![tạo distribution](/images/6.png)

#### Bước 3 — Cập nhật S3 bucket policy
Sau khi tạo xong, CloudFront console sẽ gợi ý một **bucket policy** để dán vào S3 bucket, cho phép CloudFront
(qua OAC) đọc object nhưng vẫn chặn truy cập public trực tiếp. Copy đoạn policy đó và dán vào **S3 bucket →
Permissions → Bucket policy**.


#### Bước 4 — Ghi lại domain của Distribution
Sau khi Distribution chuyển trạng thái từ **Deploying** sang **Enabled** thường mất vài phút, copy domain
dạng `dxxxxxxxxxxxxx.cloudfront.net` — bạn sẽ điền domain này vào biến `CLOUDFRONT_DOMAIN` của backend ở bước
[Triển khai backend](../../5.4-Deploy-Backend/).

![domain distribution](/images/9.png)

{{% notice tip %}}
Nếu bạn chưa muốn tạo CloudFront ngay, backend vẫn chạy được bình thường — chỉ cần để trống
`CLOUDFRONT_DOMAIN`, hệ thống sẽ tự động fallback dùng presigned URL trực tiếp từ S3.
{{% /notice %}}
