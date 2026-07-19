---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Bài học đắt giá về IAM: Từ root account đến Least Privilege

## 1. Câu chuyện mở đầu

Hồi mới học AWS, mình làm mọi thứ bằng root account vì... tiện. Tạo bucket, tạo EC2, gọi Bedrock — tất cả đều login bằng email và password của tài khoản chính. Lúc đó mình nghĩ đơn giản: "Dùng root cho nhanh, phân quyền IAM để sau, khi nào dự án lớn hơn thì tính."

Cho đến khi mình đọc được một case thật trên Reddit: một bạn commit nhầm AWS key lên GitHub, chỉ 10 phút sau đã bị charge **$8,000** vì kẻ xấu dùng key đó để spin up EC2 đào coin khắp mọi region cùng lúc.

Điều đáng sợ nhất không phải là con số $8,000. Mà là key đó là **key của root**. Không giới hạn được gì cả — kẻ xấu có toàn quyền như chính chủ tài khoản.

Đọc xong case đó, mình nhìn lại project của chính mình và nhận ra: mình đang ở đúng tình huống rủi ro y hệt vậy, chỉ là chưa ai "kích hoạt" nó thôi.

## 2. Vì sao root account nguy hiểm đến vậy?

Root account trong AWS không giống một user IAM bình thường. Nó có những đặc điểm khiến rủi ro tăng lên gấp nhiều lần:

- **Không thể giới hạn quyền**: Bạn không thể gắn policy để hạn chế root — root luôn có toàn quyền trên mọi dịch vụ, mọi region.
- **Không có khái niệm "vừa đủ dùng"**: Một IAM Role có thể chỉ cho phép đọc một bucket S3 cụ thể, nhưng root thì không có khái niệm đó.
- **Access Key của root là long-lived credential**: Một khi bị lộ, nó tồn tại mãi cho đến khi bạn tự tay vào xóa — không có cơ chế tự hết hạn.
- **Hậu quả khi bị lộ là toàn diện**: kẻ xấu có thể tạo user mới, xóa CloudTrail log, đổi billing alert, thậm chí đóng tài khoản của bạn.

Nói cách khác, dùng root cho công việc hàng ngày giống như việc luôn mang theo chìa khóa gốc mở được mọi cánh cửa trong toà nhà, chỉ để... đi lấy một ly nước.

## 3. Ba thay đổi mình áp dụng ngay sau đó

Sau khi đọc case đó, mình thay đổi hoàn toàn cách làm việc với IAM. Ba nguyên tắc dưới đây đã trở thành thói quen bắt buộc trong mọi project mình làm từ đó.

### ① Khóa root lại, không dùng hằng ngày

Root chỉ nên dùng đúng hai việc: tạo tài khoản lần đầu, và bật MFA (Multi-Factor Authentication). Sau khi làm xong hai việc đó, hãy "cất" root đi — không dùng nó để login vào console mỗi ngày, không dùng nó để tạo tài nguyên.

Mọi công việc còn lại — tạo bucket, launch EC2, gọi Bedrock — nên thực hiện thông qua **IAM user** (khi cần con người thao tác trực tiếp) hoặc **IAM Role** (khi là ứng dụng, service gọi lẫn nhau).

### ② Không bao giờ dùng Access Key nếu có thể dùng IAM Role

Đây là điểm khác biệt quan trọng nhất mà trước đó mình chưa hiểu rõ:

- **Access Key** là long-lived credential — một cặp key/secret tồn tại mãi mãi cho đến khi bạn chủ động xóa hoặc rotate. Nếu nó bị commit nhầm lên GitHub (như case ở trên), nó sẽ tồn tại "sống" ở đó cho đến khi bạn phát hiện ra.
- **IAM Role** thì khác hoàn toàn về bản chất: credential được cấp là tạm thời (temporary credentials), tự động hết hạn sau một khoảng thời gian, và AWS tự động rotate. Không có secret cố định nào để lộ ra ngoài.

Trong project thực tập của mình — **AI Marketing Voice** — backend FastAPI gọi đến Bedrock, S3, và SES đều thông qua IAM Role, với đúng 4 nhóm quyền cần thiết cho ứng dụng, không hơn không kém. Nhờ vậy, nếu chẳng may có sự cố lộ thông tin, kẻ xấu cũng chỉ có thể làm được đúng những gì role đó cho phép — không thể leo thang sang các dịch vụ khác trong tài khoản.

### ③ Principle of Least Privilege — bắt đầu từ zero, thêm dần

Một thói quen rất phổ biến (và cũng rất nguy hiểm) là gắn thẳng các policy dạng `AmazonS3FullAccess`, `AdministratorAccess`... cho tiện, khỏi phải nghĩ nhiều. Nhưng cách làm đúng là ngược lại: **bắt đầu từ zero quyền, rồi thêm dần từng permission thực sự cần thiết**.

Ví dụ, thay vì cho phép truy cập mọi bucket S3, hãy chỉ định rõ:

- Đúng **bucket** nào được truy cập (qua ARN cụ thể).
- Đúng **action** nào được thực hiện (`s3:GetObject`, `s3:PutObject`... thay vì `s3:*`).

Việc này có thể khiến bạn mất thêm 5–10 phút để setup policy chi tiết ban đầu, nhưng đổi lại là sự an tâm rất lớn về sau — nhất là khi ứng dụng của bạn được đưa lên production và có nhiều người cùng maintain.

## 4. Checklist nhanh cho người mới học AWS

Nếu bạn đang trong giai đoạn học AWS giống mình trước đây, đây là checklist mình khuyên nên làm ngay, không cần đợi "khi nào dự án lớn":

-  **Bật MFA cho root ngay hôm nay** — chỉ mất khoảng 3 phút, nhưng giảm đáng kể rủi ro nếu credential bị lộ.
-  **Không commit AWS key lên Git** — luôn dùng file `.env` để lưu credential, và nhớ thêm `.env` vào `.gitignore` trước khi commit dòng code đầu tiên.
-  **Dùng IAM Role cho EC2, Lambda, và các ứng dụng backend** — thay vì gắn Access Key trực tiếp vào code hay biến môi trường.
-  **Scope resource ARN cụ thể** — tuyệt đối tránh để `"Resource": "*"` trong policy nếu không thực sự cần thiết.

## 5. Lời kết

$8,000 trong 10 phút là một cái giá quá đắt để đánh đổi lấy sự "tiện" của việc dùng chung một tài khoản root cho mọi việc. Điều đáng nói là câu chuyện đó không hề hiếm — nó xảy ra với rất nhiều người mới học cloud, đơn giản vì IAM là một trong những phần dễ bị bỏ qua nhất khi mới bắt đầu, dù nó lại là nền tảng bảo mật quan trọng nhất của cả hệ thống.

Nếu có một điều duy nhất bạn nên mang theo sau khi đọc bài này, thì đó là: **root chỉ dùng để mở khóa lần đầu, còn lại hãy để IAM Role làm việc thay bạn.**


**Link bài đăng (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/?multi_permalinks=2211124439652516&notif_id=1784348101969974&notif_t=feedback_reaction_generic&ref=notif