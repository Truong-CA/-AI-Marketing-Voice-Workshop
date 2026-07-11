---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8

* Tích hợp Amazon Bedrock vào hệ thống Backend.
* Xây dựng chức năng tạo nội dung marketing bằng Generative AI.
* Thiết kế Prompt và xây dựng API phục vụ ứng dụng.

### Các công việc cần triển khai trong tuần này

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | Cấu hình AWS SDK và kết nối Amazon Bedrock từ Backend. | 08/06/2026 | 08/06/2026 | https://docs.aws.amazon.com/bedrock/ |
| 3 | Xây dựng module Bedrock Service để gọi Foundation Model và xử lý phản hồi. | 09/06/2026 | 09/06/2026 | https://docs.aws.amazon.com/sdk-for-python/ |
| 4 | Thiết kế Prompt Template cho các nội dung marketing như bài viết, tiêu đề và mô tả sản phẩm. | 10/06/2026 | 10/06/2026 | https://docs.aws.amazon.com/bedrock/ |
| 5 | Xây dựng API Backend sử dụng FastAPI để nhận yêu cầu và trả về nội dung do AI tạo ra. | 11/06/2026 | 11/06/2026 | https://fastapi.tiangolo.com/ |
| 6 | Kiểm thử chức năng tạo nội dung, đánh giá chất lượng kết quả và điều chỉnh Prompt để cải thiện đầu ra. | 12/06/2026 | 12/06/2026 | Tài liệu dự án |

### Kết quả đạt được tuần 8

* Kết nối thành công Backend với Amazon Bedrock thông qua AWS SDK.

* Xây dựng được module giao tiếp với Foundation Model và xử lý kết quả trả về.

* Thiết kế các Prompt Template phù hợp với mục tiêu tạo nội dung marketing.

* Hoàn thành API sinh nội dung bằng AI và kiểm thử thành công bằng các yêu cầu mẫu.

* Đánh giá và tối ưu Prompt để cải thiện chất lượng nội dung được tạo ra.

* Hoàn thành chức năng AI đầu tiên của dự án, tạo nền tảng để phát triển các tính năng tiếp theo.