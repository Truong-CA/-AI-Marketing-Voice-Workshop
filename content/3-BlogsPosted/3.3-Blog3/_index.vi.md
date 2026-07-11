---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---



# IAM User & IAM Policy: Hai Khái Niệm Nền Tảng Khi Học AWS

IAM User & IAM Policy – Hai khái niệm mà bất kỳ ai học AWS cũng nên nắm vững.

Khi mới tạo tài khoản AWS, mình chỉ biết sử dụng Root Account vì đây là tài khoản đầu tiên AWS cung cấp. Ban
đầu khá tiện, nhưng sau khi tìm hiểu, mình nhận ra đây là một trong những sai lầm phổ biến của người mới học
Cloud.

Root Account có toàn quyền trên toàn bộ tài khoản AWS. Nếu chẳng may bị lộ mật khẩu hoặc Access Key, toàn bộ
tài nguyên đều có nguy cơ bị xóa hoặc bị sử dụng trái phép.

Để giảm thiểu rủi ro này, AWS khuyến nghị chỉ sử dụng Root Account cho một số tác vụ đặc biệt và quản lý
công việc hằng ngày thông qua IAM User kết hợp với IAM Policy.

### Vì sao không nên sử dụng Root Account hằng ngày?

Root Account có toàn quyền (Full Administrative Access) đối với mọi dịch vụ và tài nguyên trong AWS. Tài
khoản này không bị giới hạn bởi bất kỳ IAM Policy nào nên có thể tạo, chỉnh sửa hoặc xóa mọi tài nguyên.

Chính vì quyền truy cập không giới hạn, Root Account cũng tiềm ẩn nhiều rủi ro. Nếu thông tin đăng nhập bị
lộ, kẻ xấu có thể xóa EC2, RDS, S3 hoặc tạo thêm tài nguyên mới, gây mất dữ liệu và phát sinh chi phí ngoài
ý muốn.

AWS khuyến nghị không sử dụng Root Account cho công việc hằng ngày. Thay vào đó, hãy tạo IAM User với quyền
phù hợp. Root Account chỉ nên dùng cho một số tác vụ đặc biệt như:
- Thay đổi phương thức thanh toán.
- Quản lý thông tin Billing.
- Đóng tài khoản AWS.
- Thực hiện các tác vụ chỉ Root Account mới được phép.

Sau khi hoàn tất thiết lập ban đầu, mình gần như không sử dụng Root Account nữa mà chuyển sang IAM User. Đây
cũng là một trong những AWS Security Best Practices quan trọng.

### IAM User – Mỗi người một tài khoản

Thay vì để nhiều người cùng sử dụng Root Account, mình tạo một IAM User riêng cho từng người.

Mỗi IAM User sẽ có:
- Username và Password riêng.
- MFA riêng để tăng cường bảo mật.
- Access Key riêng nếu sử dụng AWS CLI hoặc SDK.

Nhờ đó, mỗi người sử dụng tài khoản của mình mà không cần chia sẻ thông tin đăng nhập. Đồng thời, AWS cũng có
thể xác định chính xác ai đã thực hiện thao tác thông qua CloudTrail.

Việc quản lý cũng trở nên dễ dàng hơn. Khi có nhân viên mới chỉ cần tạo IAM User, còn khi nhân viên nghỉ
việc chỉ cần vô hiệu hóa hoặc xóa tài khoản đó.

### IAM Policy – Quyết định người dùng được phép làm gì

Nếu IAM User được ví như một nhân viên, thì IAM Policy chính là bản phân công công việc.

Ví dụ, một thực tập sinh chỉ cần quản lý EC2 để:
- Xem danh sách Instance.
- Start hoặc Stop Instance.
- Reboot Instance.

Trong khi các quyền như:
- Xóa EC2.
- Xóa Amazon RDS.
- Thay đổi IAM User hoặc IAM Policy.
- Truy cập Billing.

đều không được cấp.

IAM Policy giúp mỗi người chỉ có đúng quyền cần thiết để thực hiện công việc của mình, đồng thời hạn chế các
rủi ro bảo mật.

### Principle of Least Privilege – Chỉ cấp đúng quyền cần thiết

AWS luôn khuyến nghị áp dụng Principle of Least Privilege, nghĩa là chỉ cấp đúng quyền cần thiết, không
nhiều hơn.

Ví dụ, nếu một thực tập sinh chỉ cần kiểm tra EC2 và xem dữ liệu trên Amazon S3 thì chỉ cần cấp quyền:
- Xem EC2.
- Đọc dữ liệu S3.

Các quyền như:
- Xóa VPC.
- Xóa Amazon RDS.
- Quản lý IAM.
- Truy cập Billing.

đều không nên được cấp.

Nguyên tắc này giúp giảm thiểu rủi ro do thao tác nhầm và tăng cường bảo mật cho hệ thống.

### IAM Policy có thể được gắn ở đâu?

IAM Policy có thể được gắn vào nhiều đối tượng khác nhau.

**IAM User** — Policy chỉ áp dụng cho một người dùng cụ thể. Cách này đơn giản nhưng sẽ khó quản lý khi số
lượng người dùng tăng lên.

**IAM Group** — Đây là cách được sử dụng phổ biến trong doanh nghiệp. Thay vì cấp quyền cho từng người, chỉ
cần gắn Policy vào Group. Ví dụ:
- Developer Group: Quản lý EC2 và CloudWatch.
- Tester Group: Chỉ có quyền Read Only.
- Finance Group: Chỉ được truy cập Billing.

Khi có nhân viên mới, chỉ cần thêm vào đúng Group là đủ.

**IAM Role** — IAM Role thường được sử dụng khi một dịch vụ AWS cần thay mặt người dùng thực hiện tác vụ. Ví
dụ, EC2 cần đọc dữ liệu từ Amazon S3 thì chỉ cần gắn IAM Role cho EC2 thay vì lưu Access Key trên máy chủ.
Đây cũng là cách AWS khuyến nghị vì an toàn và dễ quản lý.

### Managed Policy và Inline Policy

AWS hỗ trợ hai loại Policy phổ biến.

**Managed Policy** có thể sử dụng lại cho nhiều User, Group hoặc Role. Ví dụ, một Policy quản lý EC2 có thể
gắn cho nhiều Developer khác nhau.

Ưu điểm:
- Có thể tái sử dụng.
- Dễ cập nhật và bảo trì.
- Khi chỉnh sửa, tất cả đối tượng đang sử dụng đều được cập nhật.

Ngoài ra, AWS cũng cung cấp sẵn nhiều AWS Managed Policies như `ReadOnlyAccess`, `AmazonS3ReadOnlyAccess` hay
`AdministratorAccess`.

**Inline Policy** chỉ gắn với một User, Group hoặc Role duy nhất. Nếu đối tượng đó bị xóa thì Policy cũng bị
xóa theo. Loại Policy này phù hợp với những trường hợp đặc biệt nhưng ít được sử dụng hơn vì khó quản lý.

### Ví dụ thực tế

Giả sử nhóm mình có ba thành viên:
- **Intern**: Chỉ được xem EC2 và CloudWatch.
- **Developer**: Được triển khai ứng dụng, quản lý EC2 và Amazon S3.
- **Administrator**: Có toàn quyền quản lý hạ tầng AWS.

Việc phân quyền theo vai trò giúp mỗi người chỉ thực hiện đúng công việc của mình, đồng thời nâng cao bảo
mật và giúp hệ thống dễ quản lý hơn.

### Kết luận

Qua quá trình tìm hiểu IAM, mình nhận ra rằng bảo mật trên AWS không chỉ nằm ở mật khẩu mạnh mà còn ở việc
cấp đúng quyền cho đúng người.

IAM User giúp tách biệt từng người dùng, IAM Policy kiểm soát chính xác các quyền được phép thực hiện, còn
nguyên tắc Least Privilege giúp giảm thiểu rủi ro và bảo vệ hệ thống hiệu quả hơn. Đây cũng là một trong
những nền tảng quan trọng khi bắt đầu làm việc với AWS.

**Link bài đăng (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206918726739754/?rdid=6SeKNuZnOr9PjQrn#
