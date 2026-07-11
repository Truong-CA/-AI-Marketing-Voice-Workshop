---
title: "Blog 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---


# Hành Trình Học AWS: Lộ Trình Và Tài Liệu Phù Hợp Cho Người Mới Bắt Đầu

Hành trình học AWS sẽ dễ dàng hơn khi bạn có trong tay những tài liệu phù hợp. Đây là những nguồn mình gợi ý
cho người mới bắt đầu.

Nếu hôm nay bạn quyết định học AWS, rất có thể điều đầu tiên khiến bạn "choáng" không phải là EC2 hay VPC, mà
là hàng nghìn tài liệu xuất hiện chỉ sau một lần tìm kiếm.

Video, blog, khóa học, tài liệu chính thức... mỗi nơi hướng dẫn một cách khác nhau, khiến mình từng không
biết nên bắt đầu từ đâu. Sau một thời gian tìm hiểu, mình nhận ra rằng chỉ cần chọn đúng nguồn tài liệu và
học theo một lộ trình phù hợp, việc học AWS sẽ trở nên dễ dàng hơn rất nhiều.

### Đừng cố học tất cả dịch vụ AWS cùng lúc

Một sai lầm mình từng mắc phải khi mới học AWS là cố gắng học quá nhiều dịch vụ cùng lúc.

Hôm nay mình học EC2, hôm sau chuyển sang Lambda, vài ngày sau lại tìm hiểu EKS. Mỗi dịch vụ đều có rất
nhiều khái niệm và cách sử dụng khác nhau, nên càng học mình càng cảm thấy rối và không nhớ được bao nhiêu.

Sau đó, mình thay đổi cách học. Thay vì học theo tên dịch vụ, mình học theo mức độ nền tảng và sự liên kết
giữa các dịch vụ.

Lộ trình mình lựa chọn là:
- **IAM** để hiểu cách quản lý người dùng và phân quyền.
- **Amazon VPC** để nắm được cách xây dựng hệ thống mạng.
- **Amazon EC2** để triển khai máy chủ.
- **Amazon S3** để lưu trữ dữ liệu.
- **Amazon RDS** để quản lý cơ sở dữ liệu.

Nhờ học theo trình tự này, mình hiểu rõ vai trò của từng dịch vụ và cách chúng kết hợp với nhau trong một hệ
thống thực tế. Điều này giúp việc học trở nên dễ dàng và hiệu quả hơn rất nhiều.

### AWS Skill Builder – Điểm khởi đầu dành cho người mới

Nếu chỉ được chọn một nguồn tài liệu để bắt đầu học AWS, mình sẽ chọn **AWS Skill Builder**.

Đây là nền tảng học trực tuyến do chính AWS phát triển, cung cấp rất nhiều khóa học miễn phí dành cho người
mới. Nội dung được xây dựng theo từng cấp độ nên khá dễ theo dõi, ngay cả khi chưa có kiến thức về Cloud.

Điều mình thích nhất là AWS Skill Builder có **Learning Plan** giúp người mới biết nên học gì trước, đi kèm
**Quiz** để kiểm tra kiến thức và một số **Hands-on Lab** miễn phí để thực hành ngay sau mỗi bài học.

Đối với người mới, đây là nguồn tài liệu rất phù hợp để xây dựng nền tảng trước khi tìm hiểu các dịch vụ
chuyên sâu hơn.

### AWS Documentation – Nguồn tài liệu mình quay lại nhiều nhất

Ban đầu mình khá ngại đọc Documentation vì nghĩ sẽ khó hiểu. Nhưng càng học mình càng nhận ra đây là nguồn
thông tin chính xác và được cập nhật nhanh nhất. Khi cần tìm hiểu một dịch vụ hoặc xác minh một tính năng,
mình luôn đọc Documentation trước rồi mới bắt đầu thực hành.

Ví dụ, khi học về IAM, thay vì tìm nhiều video khác nhau, mình đọc trực tiếp phần hướng dẫn của AWS để hiểu
cách tạo IAM User, viết IAM Policy và áp dụng nguyên tắc Least Privilege. Khi gặp các dịch vụ khác như VPC,
EC2 hay S3, mình cũng áp dụng cách học tương tự.

### AWS Well-Architected – Học cách xây dựng hệ thống đúng ngay từ đầu

AWS Well-Architected Framework giúp mình hiểu cách thiết kế hệ thống theo các khuyến nghị của AWS, thay vì
chỉ biết cách sử dụng từng dịch vụ riêng lẻ. Những nguyên tắc như Security, Reliability hay Cost Optimization
giúp mình thay đổi tư duy từ "triển khai được" sang "triển khai đúng".

### YouTube – Học qua các buổi chia sẻ từ chuyên gia AWS

Thay vì xem nhiều video từ nhiều nguồn khác nhau, mình ưu tiên các kênh chính thức như AWS Events, AWS Online
Tech Talks và AWS re:Invent. Quan trọng nhất là vừa xem vừa thực hành theo, vì chỉ xem sẽ rất nhanh quên.

Ví dụ, khi xem một video hướng dẫn tạo VPC hoặc triển khai EC2, mình sẽ mở AWS Console và làm từng bước
giống người hướng dẫn. Khi tự thao tác, mình nhớ lâu hơn rất nhiều so với chỉ xem rồi bỏ qua.

### AWS Free Tier – Học bằng cách tự tay làm

Mỗi khi học một dịch vụ mới, mình đều tạo tài nguyên thật trên AWS Free Tier, sử dụng thử rồi xóa sau khi
hoàn thành. Việc lặp lại quá trình này giúp mình nhớ kiến thức lâu hơn và tránh phát sinh chi phí không cần
thiết.

### GitHub – Nguồn tài liệu thực tế mà nhiều người bỏ qua

Khi đã có kiến thức cơ bản, mình thường tham khảo các dự án trên GitHub như Awesome AWS, AWS Samples hoặc
Terraform Examples để xem cách người khác triển khai hệ thống thực tế. Theo mình, đọc mã nguồn là cách rất
tốt để hiểu kiến thức được áp dụng vào dự án như thế nào.

### Những sai lầm mình từng mắc khi tự học AWS

Sau một thời gian tự học, mình nhận ra mình thường mắc những lỗi sau:
- Đọc quá nhiều nhưng thực hành quá ít nên nhanh quên kiến thức.
- Học quá nhiều dịch vụ cùng lúc khiến không hiểu sâu bất kỳ dịch vụ nào.
- Ngại đọc Documentation vì nghĩ khó hiểu, trong khi đây lại là nguồn tài liệu chính xác nhất.
- Quên xóa tài nguyên sau khi thực hành, dễ phát sinh chi phí không cần thiết.

Nhìn lại quá trình học, mình nhận ra rằng học AWS không cần quá nhanh. Điều quan trọng là học đúng lộ trình,
thực hành thường xuyên và tận dụng tốt những tài liệu miễn phí.

### Kết luận

Sau một thời gian học AWS, mình nhận ra rằng điều quan trọng không phải là có bao nhiêu tài liệu, mà là chọn
đúng nguồn và kiên trì thực hành. Chỉ với những tài liệu miễn phí từ AWS và cộng đồng, mình đã có thể xây
dựng nền tảng kiến thức, triển khai các bài lab và hiểu rõ hơn cách các dịch vụ hoạt động.

Nếu bạn cũng mới bắt đầu học AWS, đừng cố gắng học tất cả cùng lúc. Hãy bắt đầu từ những dịch vụ cơ bản,
thực hành thường xuyên và tận dụng các nguồn tài liệu miễn phí trước khi nghĩ đến những khóa học trả phí.

**Link bài đăng (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206923336739293/?rdid=mB3Nul56Gy964LJX#
