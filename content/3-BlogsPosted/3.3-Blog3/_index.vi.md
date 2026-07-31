---
title: "Blog 3"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AWS CONFIG VÀ CONFORMANCE PACKS

**Ngày đăng: 30-07-2026** <!-- FILL IN date -->

**Link: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2228770524554574/** <!-- FILL IN post URL -->

### Vì sao mình viết bài này

Một vấn đề nữa mà mình đoán hầu hết người mới với AWS đều gặp: khi tạo tài nguyên
trên console, bạn thường bấm lướt qua rất nhanh và bỏ qua khá nhiều tùy chọn. Sau một
thời gian, bạn không còn nhớ nổi bucket nào đang để public, instance RDS nào đang tắt
mã hóa, hay security group nào đang mở một cổng ra internet.

Điều đó dẫn mình tới **AWS Config**, dịch vụ ghi lại trạng thái cấu hình của các tài
nguyên trong một tài khoản và đối chiếu chúng với những rule đã định nghĩa sẵn. Đi kèm
với nó là **Conformance Pack**, thứ mà tài liệu của AWS mô tả là một tập hợp các rule
của AWS Config cùng các hành động khắc phục, được đóng gói để triển khai như một khối
duy nhất trong một tài khoản và một region, hoặc trên toàn bộ một tổ chức trong AWS
Organizations.

Nói đơn giản: thay vì bật từng phép kiểm tra một, bạn có thể triển khai trọn một bộ
rule cùng lúc.

### Các khái niệm chính

* **Configuration Item** - một bản ghi về trạng thái cấu hình của một tài nguyên tại
  một thời điểm. Mỗi lần một tài nguyên được tạo, sửa, hay xóa, AWS Config sinh ra một
  bản ghi mới. Đó chính là thứ tạo nên lịch sử thay đổi của tài nguyên ấy.
* **Config Rule** - một rule đánh giá xem một tài nguyên là COMPLIANT hay
  NON_COMPLIANT. AWS cung cấp một danh sách dài các managed rule, ví dụ kiểm tra xem
  một bucket S3 có chặn truy cập công khai không, hay một instance RDS đã bật mã hóa
  chưa.
* **Remediation Action** - hành động sửa chữa được thực hiện khi một tài nguyên không
  đạt. Nó có thể chạy tự động, hoặc chạy tay sau khi đã xem xét.

Một conformance pack là một file YAML gom các rule và các hành động khắc phục ấy lại
với nhau.

### Có sẵn những template nào

AWS cung cấp một số template mẫu dùng được ngay, chọn được trong console hoặc tải về
từ GitHub. Chúng chia thành vài nhóm chính:

* Operational Best Practices cho từng dịch vụ riêng lẻ như S3, RDS, IAM, EC2.
* Operational Best Practices bám theo các trụ cột của AWS Well-Architected Framework.
* Các template ánh xạ tới những chuẩn quen thuộc như CIS AWS Foundations Benchmark,
  NIST 800-53, HIPAA, và PCI DSS.

Có một điểm AWS nêu rất rõ trong tài liệu, và mình nghĩ là quan trọng: những template
mẫu này **không được thiết kế để bảo đảm tuân thủ bất kỳ chuẩn quản trị cụ thể nào**.
Chúng không thay thế được quy trình nội bộ và không bảo đảm bạn sẽ vượt qua một đợt
đánh giá tuân thủ. Chúng là một công cụ hỗ trợ rà soát, không phải một chứng chỉ.

### Các bước triển khai

1. Bật AWS Config ở region bạn muốn dùng, chọn các loại tài nguyên cần ghi lại, và
   tạo một IAM role cấp cho Config quyền đọc cấu hình.
2. Mở AWS Config Console, chọn **Conformance packs**, rồi chọn **Deploy conformance
   pack**.
3. Chọn nguồn template. *Use sample template* lấy một cái từ danh sách có sẵn.
   *Template is ready* dành cho template của riêng bạn, có thể lấy từ S3, từ một SSM
   document, hoặc tải lên trực tiếp. Lưu ý rằng một template lớn hơn 50 KB bắt buộc
   phải lưu trong S3.
4. Đặt tên cho conformance pack. Tên phải là duy nhất, tối đa 256 ký tự chữ và số, có
   thể chứa dấu gạch nối nhưng không chứa khoảng trắng.
5. Cung cấp tham số nếu cần. Nhiều template nhận tham số để điều chỉnh ngưỡng, ví dụ
   tuổi tối đa của một access key. Đây là cách tốt để tùy biến một template mà không
   phải sửa trực tiếp vào nó.
6. Triển khai, rồi xem lại dashboard, nơi hiển thị tỉ lệ tuân thủ, những rule không
   đạt, và chính xác những tài nguyên nào đang vi phạm.

Một lưu ý nữa từ tài liệu: hãy kiểm tra danh sách các rule có sẵn ở region bạn định
triển khai, bởi không phải rule nào cũng tồn tại ở mọi region. Nếu một template chứa
rule không được hỗ trợ, có thể bạn sẽ phải sửa nó trước khi triển khai.

### Ưu điểm

* Cả một bộ rule có thể được triển khai cùng lúc, thay vì bật thủ công từng cái một.
* Đã có sẵn template cho từng dịch vụ riêng lẻ và cho các chuẩn nổi tiếng, nên bạn
  không phải tự nghĩ ra danh sách kiểm tra.
* Template là YAML, nên có thể commit vào Git và quản lý như code.
* Nó triển khai được trên nhiều tài khoản trong AWS Organizations để tạo ra một mặt
  bằng chung.
* Lịch sử cấu hình được lưu lại, rất hữu ích khi bạn cần biết một thiết lập đã bị đổi
  vào lúc nào.

### Những điểm đáng biết

**Về chi phí.** Đây là phần mình nghĩ sinh viên nên đọc kỹ trước khi bật nó lên. AWS
Config tính phí trên ba thành phần: số configuration item được ghi lại, số lần đánh
giá rule, và số lần đánh giá conformance pack.

Theo trang giá, một configuration item tốn 0,003 USD cho mỗi bản ghi ở chế độ ghi liên
tục. Việc đánh giá rule và đánh giá conformance pack tốn 0,001 USD cho 100.000 lần đầu
tiên mỗi region mỗi tháng, sau đó giảm dần theo bậc.

Điều đáng lưu ý là các lần đánh giá **không nằm trong free tier**, nghĩa là chúng bị
tính tiền ngay từ lần đầu tiên. Một conformance pack chứa vài chục rule, chạy trên
nhiều tài nguyên, có thể sinh ra số lần đánh giá lớn hơn nhiều so với hình dung ban
đầu của bạn. Nếu bạn chỉ dùng để học, mình sẽ giới hạn các loại tài nguyên được ghi
lại và nhớ xóa conformance pack sau khi thử xong.

**Về khắc phục tự động.** Mình sẽ không bật khắc phục tự động ngay từ đầu. Một hành
động sửa chữa chạy nhầm chỗ có thể ảnh hưởng tới những tài nguyên đang được sử dụng.
Chạy ở chế độ thủ công trước thì an toàn hơn.

**Về cách đọc kết quả.** Tỉ lệ tuân thủ 100 phần trăm chỉ có nghĩa là các rule trong
pack đó đều đạt. Nó không có nghĩa là hệ thống của bạn an toàn. Ngược lại, một rule
không đạt cũng chưa chắc đã là vấn đề, bởi nó còn tùy vào bối cảnh sử dụng. Điều quan
trọng là hiểu mỗi rule thực sự kiểm tra cái gì.

### Khi nào nên dùng

* Thiết lập một mặt bằng cấu hình chung trên nhiều tài khoản hoặc nhiều thành viên
  trong nhóm.
* Rà soát nhanh xem môi trường hiện tại đã trôi lệch khỏi thực hành tốt ở những chỗ
  nào.
* Cần lịch sử thay đổi cấu hình để phục vụ kiểm toán hoặc điều tra sự cố.
* Muốn tự động sửa một loại lỗi cấu hình thường xuyên lặp lại.

Với một tài khoản cá nhân chỉ có vài tài nguyên, mình sẽ bắt đầu bằng một hai Config
rule riêng lẻ cho quen, rồi chuyển sang conformance pack sau.

### Kết luận

AWS Config trả lời được hai câu hỏi mà trước đây mình vẫn còn mơ hồ: các tài nguyên
của mình hiện đang được cấu hình ra sao, và cấu hình đó đã thay đổi thế nào theo thời
gian.

Conformance pack có nghĩa là mình không phải tự nghĩ ra danh sách những thứ đáng kiểm
tra, mà có thể bắt đầu từ một bộ rule mà AWS đã tập hợp sẵn.

### Tài liệu tham khảo

* AWS Config - Conformance Packs:
  https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html
* AWS Config - Deploying Conformance Packs:
  https://docs.aws.amazon.com/config/latest/developerguide/conformance-pack-deploy.html
* AWS Config - Conformance Pack Sample Templates:
  https://docs.aws.amazon.com/config/latest/developerguide/conformancepack-sample-templates.html
* AWS Config - List of AWS Config Managed Rules:
  https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html
* AWS Config Pricing:
  https://aws.amazon.com/config/pricing/

<!-- Screenshot of the published post goes in
     static/images/3-BlogsPosted/3.3-Blog3/ and is referenced like:
-->

![Bài viết đã đăng trên trang cộng đồng AWS Study Group](/images/3-BlogsPosted/3.3-Blog3/post.png)
