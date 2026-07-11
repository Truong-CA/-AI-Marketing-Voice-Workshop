---
title : "Dọn dẹp tài nguyên"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

Để tránh phát sinh chi phí AWS sau khi hoàn thành workshop, hãy xóa toàn bộ tài nguyên đã tạo theo đúng thứ tự
sau (xóa CloudFront trước khi xóa S3, vì CloudFront còn đang tham chiếu tới bucket):

#### 1. Vô hiệu hoá và xóa CloudFront Distribution
1. **CloudFront console** → chọn distribution đã tạo → **Disable**.
2. Chờ trạng thái chuyển thành **Disabled** (có thể mất vài phút).
3. Sau khi đã Disabled, chọn distribution → **Delete**.
4. Vào **Origin access** → xóa Origin Access Control đã tạo ở bước 5.3.1 (không còn distribution nào dùng).

#### 2. Xóa rỗng và xóa S3 bucket
```
aws s3 rm s3://ai-marketing-voice-<ten-cua-ban>-demo --recursive
aws s3api delete-bucket --bucket ai-marketing-voice-<ten-cua-ban>-demo
```
Hoặc qua console: **S3** → chọn bucket → **Empty** → sau đó **Delete**. Nhớ gỡ bucket policy đã dán cho
CloudFront nếu bucket policy đó chặn việc xoá bucket.

#### 3. Xóa CloudWatch Log Group
1. **CloudWatch console** → **Log groups** → chọn `/ai-marketing-voice/backend`.
2. **Actions** → **Delete log group(s)**.

#### 4. Xóa (hoặc giữ lại) SES identity
- Nếu email verify trong SES là email cá nhân bạn vẫn muốn dùng sau này, có thể **giữ lại** identity đó (SES
  verified identity không tính phí khi không gửi email).
- Nếu muốn dọn sạch hoàn toàn: **SES console** → **Verified identities** → chọn identity → **Delete**.

#### 5. Xóa IAM user/policy
1. **IAM console** → **Users** → chọn `ai-marketing-voice-backend`.
2. Xóa access key đã tạo để phát triển local.
3. Gỡ và xóa policy least-privilege đã tạo ở bước 5.3.
4. Xóa IAM user.

#### 6. (Nếu đã deploy) Tắt các tài nguyên compute
Nếu bạn đã triển khai backend lên EC2/App Runner/ECS để kiểm thử, hãy dừng và xóa tài nguyên đó để không
phát sinh chi phí.

#### 7. Thu hồi quyền truy cập model Bedrock (tùy chọn)
Amazon Bedrock tính phí theo lượt gọi (on-demand), nên việc chỉ bật quyền truy cập model không phát sinh chi
phí liên tục — bước này chỉ cần thiết nếu tổ chức của bạn yêu cầu thu hồi quyền không dùng đến.

#### Kiểm tra lại
- [ ] CloudFront distribution không còn tồn tại (đã Disable rồi Delete)
- [ ] S3 bucket không còn tồn tại
- [ ] CloudWatch Log Group không còn tồn tại
- [ ] Đã quyết định giữ hay xoá SES verified identity
- [ ] IAM user/policy không còn tồn tại
- [ ] Không còn tài nguyên EC2/App Runner/ECS nào đang chạy
- [ ] `aws s3 ls` / IAM console / CloudFront console / CloudWatch console đều xác nhận tài khoản đã sạch

![xác nhận dọn dẹp](/images/5-Workshop/5.6-Cleanup/cleanup-confirm.png)
