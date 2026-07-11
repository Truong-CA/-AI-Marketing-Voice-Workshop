---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Di chuyển dữ liệu và tiết kiệm chi phí ở quy mô lớn với Amazon S3 File Gateway

Nếu bạn đang quản lý hệ thống máy chủ lưu trữ nội bộ (on-premises) và muốn di chuyển một lượng lớn dữ liệu lên đám mây, chắc hẳn bạn đã hoặc sẽ gặp phải bài toán đau đầu này: Làm sao để di chuyển mà vẫn giữ nguyên cấu trúc cũng như ngày giờ tạo (Create Date) gốc của các tệp tin? Việc dữ liệu bị "làm mới" thời gian lúc tải lên đám mây không chỉ làm mất đi thông tin lịch sử quan trọng mà còn tước đi cơ hội tận dụng các chính sách tự động phân tầng để tiết kiệm chi phí cho những dữ liệu đã cũ.
1. Vấn đề: Khi di chuyển dữ liệu lên Amazon S3 thông qua S3 File Gateway, hệ thống sẽ tự động lấy "Ngày sửa đổi" (Modified Date) từ tệp nguồn để gán thành "Ngày tạo" (Create Date) mới trên S3. Sự thay đổi này làm tuổi thọ của tệp bị "reset", khiến các Chính sách Vòng đời (S3 Lifecycle Policy) không thể tự động phân loại và đẩy những dữ liệu cũ vào các lớp lưu trữ giá rẻ dựa trên tuổi đời thật của chúng, gây lãng phí ngân sách lưu trữ của doanh nghiệp.
2. Bước ngoặt: Giải pháp tự động hóa bảo toàn siêu dữ liệu Thay vì để AWS làm mới thời gian, bạn có thể kết hợp Robocopy, Amazon S3 File Gateway, và AWS Lambda để di chuyển dữ liệu mà vẫn giữ nguyên vẹn các siêu dữ liệu (metadata) ban đầu. Hàm Lambda sẽ tự động đọc ngày tháng thực sự của tệp và chuyển chúng vào đúng lớp lưu trữ phù hợp (như Glacier Instant Retrieval hay Glacier Deep Archive) ngay khi dữ liệu vừa hạ cánh lên S3.
3. Tại sao đây là giải pháp tối ưu cho việc di chuyển dữ liệu? Giải pháp này mang lại các lợi ích cốt lõi giúp tối ưu hóa chi phí:
Bảo toàn thông tin gốc: Giữ nguyên thời gian tạo, sửa đổi và các quyền truy cập NTFS gốc của tệp trong quá trình chuyển đổi.
Tối ưu hóa chi phí ở quy mô lớn: Tự động đẩy các tệp tin cũ vào các lớp lưu trữ S3 có chi phí thấp dựa trên tuổi thọ thật sự của chúng thay vì ngày chúng được đưa lên đám mây.
Hỗ trợ kiến trúc lai (Hybrid Cloud): Doanh nghiệp vẫn có thể truy cập dữ liệu đám mây từ các ứng dụng on-premises thông qua các giao thức quen thuộc như SMB và NFS mà không cần phải thay đổi môi trường hiện tại.
Tự động hóa hoàn toàn: Tận dụng khả năng tích hợp sâu của Lambda với S3 để tự động thực thi các hành động chuyển đổi lớp lưu trữ mà không cần sự can thiệp thủ công.
4. Cơ chế hoạt động đằng sau Hệ thống được vận hành thông qua sự phối hợp của 3 thành phần chính:
Robocopy: Sử dụng công cụ này với các tham số chuyên dụng (như /TIMEFIX để đồng bộ thời gian, /SECFIX để sao chép quyền truy cập) để chép dữ liệu vào file share của S3 File Gateway nhằm giữ lại các thuộc tính gốc.
Amazon S3 File Gateway: Khi tự động đưa tệp lên S3, gateway sẽ lưu trữ thời gian gốc (thông số mtime) dưới dạng siêu dữ liệu do người dùng xác định (user-defined metadata) đính kèm vào các object trên S3.
AWS Lambda: Bất cứ khi nào có lệnh PUT để ghi một object lên S3, Lambda sẽ được kích hoạt, trích xuất thông số mtime (dưới định dạng Unix time) từ siêu dữ liệu và gọi API để chuyển tệp sang lớp lưu trữ chi phí thấp nếu đạt đủ điều kiện về tuổi thọ.
5. Một vài lưu ý nhỏ khi triển khai Để cài đặt và di chuyển thành công, bạn cần chú ý:
Cẩn trọng với tốc độ của Robocopy: Quá trình sao chép có thể chậm tùy thuộc vào số lượng tệp và tốc độ ổ cứng hệ thống. Bạn có thể sử dụng cờ /L (tùy chọn dry run) để xem trước các thay đổi công cụ sẽ thực hiện trước khi quyết định sao chép thật.
Lựa chọn nền tảng cho File Gateway: S3 File Gateway hỗ trợ chạy dưới dạng máy ảo (VMware, Microsoft Hyper-V, Linux KVM) hoặc bạn cũng có thể sử dụng nó như một thiết bị phần cứng chuyên dụng ngay tại trung tâm dữ liệu của mình.
Link gốc: https://aws.amazon.com/vi/blogs/storage/data-migrations-at-scale-with-amazon-s3-file-gateway
**Link bài đăng (AWS Study Group):** https://www.facebook.com/share/p/1BVxJjTEmi/?
