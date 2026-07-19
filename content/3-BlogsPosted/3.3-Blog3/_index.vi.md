---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AWS Lambda MicroVMs — Khi Serverless có thêm sức mạnh của VM

## 1. AWS Lambda MicroVMs là gì?

AWS vừa ra mắt **AWS Lambda MicroVMs** — một tài nguyên tính toán serverless hoàn toàn mới, tách biệt với Lambda Functions truyền thống, mang lại môi trường sandbox cô lập ở cấp độ VM: không chia sẻ kernel, không chia sẻ tài nguyên giữa các session. Đây được xây dựng dựa trên Firecracker — chính công nghệ ảo hoá đã đứng sau hơn 15 nghìn tỷ lượt gọi Lambda mỗi tháng từ trước đến nay.

Nói ngắn gọn: nếu Lambda Functions cho bạn "chạy một đoạn code rồi biến mất", thì Lambda MicroVMs cho bạn hẳn một chiếc máy ảo riêng — khởi động gần như tức thì, có thể giữ trạng thái nhiều giờ liền, nhưng vẫn hoàn toàn serverless, không phải quản lý server nào cả.

## 2. Vấn đề Lambda truyền thống đang gặp phải

Lambda cũ chạy các function trong môi trường chia sẻ — nhanh, rẻ, nhưng không đủ cô lập cho các workload nhạy cảm hoặc cần trạng thái lâu dài. Điều này tạo ra một tình huống khó xử cho nhiều team: họ buộc phải chọn một trong hai hướng, dù bản chất công việc thực ra rất đơn giản — "chạy một đoạn code của người dùng hoặc AI, rồi thôi":

- Dùng **Lambda Functions**: nhanh, rẻ, không cần quản lý hạ tầng — nhưng không có trạng thái lâu dài, thời gian chạy tối đa chỉ 15 phút, và mức độ cô lập không đủ sâu cho những use case nhạy cảm.
- Dùng **container hoặc EC2**: cô lập tốt hơn, giữ được trạng thái — nhưng phải tự quản lý toàn bộ vòng đời hạ tầng: cluster, capacity, patching, networking... và vẫn tốn tiền ngay cả lúc không có traffic (idle).

Bài toán này càng trở nên rõ ràng hơn khi làn sóng ứng dụng đa người dùng (multi-tenant) chạy code do người dùng hoặc AI sinh ra ngày càng nhiều — coding assistant, AI agent, sandbox thực thi code, nền tảng phân tích dữ liệu... Mỗi người dùng hoặc mỗi phiên làm việc cần một môi trường thực thi riêng biệt, để lỗi hoặc mã độc của một người không ảnh hưởng đến người khác đang chạy song song.

## 3. Lambda MicroVMs giải quyết điều đó như thế nào?

Lambda MicroVMs cung cấp một môi trường sandbox cô lập hoàn toàn ở cấp VM, không có kernel hay tài nguyên dùng chung giữa các session. Cơ chế hoạt động khá thú vị, theo mô hình "tạo image trước, launch sau":

1. **Tạo MicroVM Image**: bạn đóng gói code ứng dụng cùng một Dockerfile vào file zip, tải lên Amazon S3, rồi gọi Lambda API để tạo image. Lambda sẽ chạy Dockerfile đó, khởi tạo ứng dụng, và chụp lại một snapshot của toàn bộ trạng thái memory và disk khi ứng dụng đã sẵn sàng.
2. **Launch MicroVM từ image**: mỗi khi cần một môi trường cô lập mới — cho một phiên người dùng, một job, hay một sandbox — bạn gọi `run-microvm`. Lambda sẽ khởi động MicroVM đó từ snapshot có sẵn, thay vì phải "cold boot" từ đầu, nên tốc độ khởi động gần như tức thì.
3. **Kết nối trực tiếp**: mỗi MicroVM được cấp một HTTPS endpoint riêng, hỗ trợ các giao thức phổ biến như HTTP/2, gRPC, WebSockets — không cần load balancer, không cần tự dựng networking.
4. **Suspend/Resume theo nhu cầu**: khi người dùng không tương tác nữa, MicroVM sẽ tự động suspend sau một khoảng thời gian idle có thể cấu hình, đồng thời lưu lại toàn bộ trạng thái memory và disk. Khi có traffic quay lại, MicroVM resume và tiếp tục đúng tại điểm đã dừng — trạng thái được bảo toàn tối đa lên đến 8 giờ.

Toàn bộ quá trình này không đòi hỏi bạn phải quản lý cluster, capacity, hay vòng đời VM theo cách thủ công như khi tự vận hành EC2/container.

## 4. Tại sao đây là một bước tiến lớn?

Trước đây, khi xây dựng một ứng dụng cần cấp môi trường riêng cho từng người dùng hoặc từng job, bạn gần như luôn phải đánh đổi giữa ba yếu tố: **tốc độ khởi động**, **mức độ cô lập**, và **khả năng giữ trạng thái**. Không có lựa chọn nào cho cả ba cùng lúc:

| | Lambda Functions | EC2 / ECS | Lambda MicroVMs |
|---|---|---|---|
| Tốc độ khởi động | Rất nhanh | Chậm hơn, phải tự quản lý | Gần như tức thì (resume từ snapshot) |
| Mức độ cô lập | Chia sẻ tài nguyên | Cô lập tốt | Cô lập cấp VM, không chia sẻ kernel |
| Giữ trạng thái | Không (tối đa 15 phút/lần chạy) | Có, nhưng tự quản lý | Có, tới 8 giờ, tự động suspend/resume |
| Quản lý hạ tầng | Không cần | Cần (cluster, patch, capacity...) | Không cần |
| Chi phí lúc idle | Không tốn (vì không chạy) | Vẫn tốn | Giảm nhờ tự suspend |

Lambda MicroVMs lấp đầy đúng khoảng trống ở giữa: bạn có được sự cô lập cấp VM và khả năng giữ trạng thái như khi tự vận hành EC2, nhưng vẫn giữ được trải nghiệm "không cần lo về server" đặc trưng của serverless.

Đáng chú ý, đây không phải là một bản cập nhật của Lambda Functions, mà là một **resource hoàn toàn mới** trong hệ sinh thái Lambda, với API riêng biệt (`lambda-microvms`). Lambda Functions vẫn là lựa chọn phù hợp cho các workload event-driven, request-response ngắn hạn; còn Lambda MicroVMs được thiết kế riêng cho các ứng dụng multi-tenant cần cấp môi trường thực thi cô lập, lâu dài cho từng người dùng hoặc từng phiên làm việc. Hai loại tài nguyên này bổ trợ cho nhau chứ không thay thế nhau.

## 5. Ứng dụng thực tế

- **AI agent với phiên làm việc kéo dài**: các coding assistant hay AI agent hiện đại thường cần chạy code do LLM sinh ra một cách lặp đi lặp lại, đồng thời giữ nguyên ngữ cảnh giữa các lần lặp, và có thể nhanh chóng khởi tạo/hủy môi trường để thử nghiệm các nhánh code khác nhau (ví dụ trong reinforcement learning). Lambda MicroVMs đáp ứng đúng nhu cầu này.
- **Sandbox thực thi code người dùng**: các nền tảng như online judge, code playground, hay công cụ scan lỗ hổng bảo mật cần môi trường cô lập mạnh giữa các phiên, có thể scale nhanh khi có nhiều request đồng thời, và đôi khi cần quyền hệ điều hành ở mức cao hơn bình thường.
- **CI/CD**: các pipeline CI/CD cần môi trường build/test tạm thời, cô lập, khởi động nhanh và có thể huỷ ngay sau mỗi lần chạy — MicroVMs cung cấp một môi trường cô lập riêng cho từng giai đoạn (stage) của pipeline, không chia sẻ trạng thái giữa các job.
- **Xử lý dữ liệu nhạy cảm đa tenant**: các tác vụ phân tích dữ liệu cần đảm bảo tuyệt đối không có rò rỉ thông tin giữa các tenant khác nhau trên cùng một nền tảng.

## 6. Điểm cần lưu ý

Tại thời điểm ra mắt, Lambda MicroVMs khả dụng ở 5 khu vực: US East (N. Virginia), US East (Ohio), US West (Oregon), Asia Pacific (Tokyo), và Europe (một khu vực châu Âu). Cấu hình hỗ trợ hiện tại là kiến trúc ARM64, tối đa 16 vCPU, 32 GB bộ nhớ và 32 GB dung lượng đĩa cho mỗi instance. Image nền tảng mặc định dựa trên Amazon Linux 2023 bản tối giản, và bạn có thể tuỳ biến thêm thông qua Dockerfile của riêng mình.

Về chi phí, Lambda MicroVMs tính phí theo giây, dựa trên nhiều thành phần khác nhau (tài nguyên compute đang chạy, dung lượng lưu trữ snapshot...), nên trước khi đưa vào production, bạn nên xem kỹ trang pricing chính thức của AWS Lambda để ước lượng chi phí phù hợp với mô hình sử dụng của mình — đặc biệt nếu có nhiều phiên chạy song song và thời gian giữ trạng thái kéo dài.

## 7. Lời kết

Lambda MicroVMs không phải là một bản nâng cấp nhỏ của Lambda, mà là lời giải cho một bài toán mà rất nhiều team gặp phải trong vài năm gần đây: làm sao cấp cho mỗi người dùng, mỗi job một môi trường thực thi riêng, đủ cô lập, đủ nhanh, mà không phải tự vận hành hạ tầng. Với sự bùng nổ của AI agent và các ứng dụng chạy code do người dùng/AI sinh ra, đây là một trong những bước tiến đáng theo dõi nhất của hệ sinh thái serverless trên AWS trong thời gian tới.
**Link bài đăng (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/posts/2211115136320113/?notif_id=1784345800431355&notif_t=group_post_approved&ref=notif
