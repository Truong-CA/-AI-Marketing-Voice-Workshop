---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Amazon Bedrock AgentCore Payments 
Amazon Bedrock AgentCore Payments là gì?
Ngày 07/05/2026, AWS công bố bản preview của Amazon Bedrock AgentCore Payments — một tính năng mới nằm trong Amazon Bedrock AgentCore. Điểm đặc biệt: tính năng này cho phép AI Agent tự động thanh toán để truy cập các API, MCP server, nội dung web trả phí, hoặc thậm chí thuê một Agent khác làm việc — mà không cần con người can thiệp vào từng giao dịch.
Vì sao AWS lại làm tính năng này?
Theo bài viết, ngày càng nhiều dịch vụ chuyển sang mô hình tính phí theo từng lượt gọi, với chi phí mỗi lần có khi chỉ vài phần nghìn của 1 đô la. Trước đây, nếu muốn Agent tự thanh toán cho các dịch vụ như vậy, đội ngũ phát triển phải tự xây dựng quan hệ thanh toán riêng với từng nhà cung cấp, tự quản lý thông tin xác thực, tự đặt giới hạn chi tiêu — một khối lượng công việc kỹ thuật không nhỏ, và rủi ro cũng cao vì sai sót ở đây đồng nghĩa với việc mất tiền thật.
Cách hoạt động cơ bản:
Một vài điểm mình thấy đáng chú ý về cách tính năng này vận hành:
Nhà phát triển kết nối Agent với một ví thanh toán, người dùng cuối nạp tiền vào ví bằng stablecoin hoặc thẻ.
Trước khi Agent được phép giao dịch, người dùng phải chủ động cấp quyền sử dụng ví đó.
Mỗi phiên làm việc đều có giới hạn chi tiêu riêng — Agent không bao giờ có quyền truy cập tiền một cách không giới hạn.
Khi Agent gặp một endpoint tính phí và nhận phản hồi yêu cầu thanh toán, hệ thống sẽ tự động xác thực ví, thực hiện thanh toán bằng stablecoin, và tiếp tục lấy nội dung — toàn bộ diễn ra ngay trong vòng lặp xử lý của Agent, không làm gián đoạn luồng suy luận.
Toàn bộ giao dịch đều có thể theo dõi lại qua log và observability có sẵn của AgentCore.
Cơ chế này dựa trên x402 — một giao thức thanh toán mở, hoạt động trên nền HTTP, do Coinbase phát triển.
Ứng dụng thực tế:
Một Agent nghiên cứu tài chính có thể tự trả phí để lấy dữ liệu thị trường thời gian thực hoặc bài báo trả phí, thay mặt người dùng cuối.
Một coding Agent có thể tự gọi và trả phí cho các API chuyên biệt, ví dụ một private package registry hay môi trường sandbox để chạy thử code.
Xa hơn, AWS hình dung Agent có thể tự đặt vé máy bay, đặt phòng khách sạn, hoàn tất giao dịch mua hàng thay người dùng.