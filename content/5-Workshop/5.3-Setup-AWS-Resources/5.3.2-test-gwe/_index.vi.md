---
title : "Cấu hình SES & CloudWatch Logs"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

#### Phần 1 — Verify địa chỉ email gửi trong Amazon SES

1. Mở **Amazon SES console** → **Verified identities** → **Create identity**.
2. Chọn **Email address**, nhập email bạn muốn dùng để gửi email chào mừng (ví dụ chính email của bạn).
3. Nhấn **Create identity** — AWS sẽ gửi một email xác nhận đến địa chỉ đó.

4. Mở hộp thư, bấm vào link xác nhận trong email từ AWS.
5. Quay lại SES console, xác nhận trạng thái identity chuyển thành **Verified**.

![verified](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/10.png)
![verified](https://truong-ca.github.io/-AI-Marketing-Voice-Workshop/images/11.png)

{{% notice tip %}}
Tài khoản SES mới mặc định ở chế độ **Sandbox** — chỉ gửi được email tới các địa chỉ **đã verify**. Với mục
đích demo/workshop thì việc verify chính email của bạn là đủ để test. Muốn gửi cho người dùng bất kỳ thi không
cần verify trước, bạn cần vào **Account dashboard** → **Request production access** và điền form, thường
được duyệt trong vài giờ đến 1 ngày.
{{% /notice %}}

Ghi lại địa chỉ email đã verify — đây chính là giá trị bạn sẽ điền vào biến `SES_SENDER_EMAIL` của backend.

#### Phần 2 — Tạo CloudWatch Log Group

Backend đã được viết để **tự tạo Log Group** khi khởi động nếu chưa tồn tại (nhờ IAM quyền
`logs:CreateLogGroup` đã cấp ở bước trước), nên bạn **không bắt buộc** phải tạo trước bằng tay. Tuy nhiên, để
xác nhận đã cấu hình đúng, bạn có thể tạo trước và kiểm tra:

1. Mở **Amazon CloudWatch console** → **Log groups** → **Create log group**.
2. Đặt tên khớp với giá trị bạn sẽ dùng ở biến `CLOUDWATCH_LOG_GROUP`, ví dụ `/ai-marketing-voice/backend`.
3. Retention: chọn một giá trị hợp lý cho demo, ví dụ **3 days** hoặc **1 week**, để tránh log tồn đọng lâu
   gây phát sinh chi phí lưu trữ.



#### Kiểm tra lại
- [ ] SES identity đã ở trạng thái **Verified**
- [ ] Đã ghi lại địa chỉ email verify để dùng cho `SES_SENDER_EMAIL`
- [ ] Log group `/ai-marketing-voice/backend` đã tồn tại trong CloudWatch
- [ ] Hiểu rõ giới hạn Sandbox của SES trước khi test đăng ký với các email khác
