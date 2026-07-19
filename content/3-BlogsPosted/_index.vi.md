---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


###  [Blog 1 - Sơ lược về IAM: Từ root account đến Least Privilege](3.1-Blog1/)
Blog này kể lại một bài học đắt giá khi mới học AWS: chỉ vì lỡ commit nhầm AWS key của root account lên GitHub, một người dùng đã bị charge $8,000 chỉ trong 10 phút vì kẻ xấu lợi dụng key đó để spin up EC2 đào coin khắp mọi region. Từ câu chuyện đó, bài viết chia sẻ ba thay đổi quan trọng trong cách làm việc với IAM: khóa root lại và chỉ dùng để bật MFA, ưu tiên IAM Role thay vì Access Key vì credential tự hết hạn và tự rotate, cùng với việc áp dụng Principle of Least Privilege — bắt đầu từ zero quyền rồi thêm dần thay vì gắn sẵn các policy full access cho tiện.

###  [Blog 2 - Sơ lược về Amazon Bedrock AgentCore Payments ](3.2-Blog2/)
Blog này giới thiệu một AI Agent tự rút ví trả tiền để lấy dữ liệu, gọi API, hay thậm chí thuê một Agent khác làm việc thay mình — hoàn toàn không cần con người bấm nút xác nhận? Đó chính xác là những gì AWS vừa giới thiệu với Amazon Bedrock AgentCore Payments. Trong bối cảnh ngày càng nhiều dịch vụ chuyển sang tính phí theo từng lượt gọi với chi phí chỉ vài phần nghìn đô la, việc để Agent tự quản lý ví, tự thanh toán trong giới hạn ngân sách được cấp phép không còn là viễn cảnh xa vời, mà đã trở thành một tính năng thực sự, dựa trên giao thức mở x402 do Coinbase phát triển.

###  [Blog 3 - AWS Lambda MicroVMs — Khi Serverless có thêm sức mạnh của VM](3.3-Blog3/)
Blog này giới thiệu AWS Lambda MicroVMs — một tài nguyên tính toán serverless hoàn toàn mới, cung cấp môi trường sandbox cô lập ở cấp độ VM, không chia sẻ kernel hay tài nguyên giữa các session. Nếu Lambda truyền thống buộc bạn phải đánh đổi giữa tốc độ, chi phí và mức độ cô lập/giữ trạng thái — khiến nhiều team phải "cắn răng" chuyển sang container hay EC2 chỉ vì cần kiểm soát sâu hơn — thì Lambda MicroVMs, được xây dựng trên nền Firecracker, lấp đầy đúng khoảng trống đó: khởi động gần như tức thì, giữ trạng thái tới 8 giờ, toàn quyền kiểm soát vòng đời, mà vẫn không phải quản lý bất kỳ hạ tầng nào.
