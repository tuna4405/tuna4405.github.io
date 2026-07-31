---
title: "Blog 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AWS BUDGETS VÀ COST ANOMALY DETECTION

**Ngày đăng: 30-07-2026** <!-- FILL IN date -->

**Link: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2229088634522763/** <!-- FILL IN post URL -->

### Vì sao mình viết bài này

Một vấn đề mà mình nghĩ đa số sinh viên đều gặp từ sớm: bạn tạo tài nguyên ra để thực
hành, rồi quên xóa chúng đi. Free Tier có giới hạn, và một khi vượt qua thì các khoản
phí bắt đầu cộng dồn mà chẳng có tín hiệu nào báo cho bạn. Bạn chỉ biết khi mở hóa đơn
vào cuối tháng.

Tìm hiểu kỹ hơn, mình thấy có hai công cụ trong nhóm quản lý chi phí của AWS xử lý
chuyện này từ hai hướng khác nhau. **AWS Budgets** cho phép bạn đặt một ngưỡng chi
tiêu và được thông báo khi mức chi thực tế hoặc mức dự báo vượt qua ngưỡng đó. **AWS
Cost Anomaly Detection** dùng machine learning để học thói quen chi tiêu của một tài
khoản rồi cảnh báo khi mức chi lệch khỏi thói quen ấy.

Nói đơn giản, Budgets trả lời câu hỏi "mình đã vượt qua giới hạn mình đặt ra chưa",
còn Cost Anomaly Detection trả lời "có khoản chi nào trông bất thường không". Hai công
cụ bổ trợ cho nhau.

### Các khái niệm chính

* **Budget** - một ngưỡng chi phí hoặc mức sử dụng do bạn định nghĩa. AWS Budgets hỗ
  trợ cost budget, usage budget, và cả budget theo dõi mức tận dụng lẫn độ phủ của
  Reserved Instances và Savings Plans.
* **Alert** - thông báo được gửi đi khi chạm ngưỡng. Nó có thể đặt theo mức chi thực
  tế hoặc mức chi dự báo. Cảnh báo theo dự báo là loại hữu ích hơn, bởi nó kích hoạt
  khi AWS dự đoán rằng bạn *sẽ* vượt ngưỡng, chừa lại thời gian để hành động.
* **Budget Action** - một phản ứng tự động khi ngưỡng bị vượt qua. Một action có thể
  gắn một IAM policy, gắn một Service Control Policy, hoặc dừng các instance EC2 và
  RDS.
* **Cost Monitor** - đối tượng của Cost Anomaly Detection, dùng để định nghĩa thứ
  đang được theo dõi.
* **Alert Subscription** - phần cấu hình về việc ai nhận cảnh báo, nhận với tần suất
  nào, và ở ngưỡng bao nhiêu.

### Cost Anomaly Detection có thể theo dõi những gì

Chi tiêu có thể được phân mảnh theo bốn chiều: AWS Services, Linked Accounts, Cost
Allocation Tags, và Cost Categories.

Với một tài khoản cá nhân, monitor theo AWS Services là lựa chọn đúng. Loại monitor
này tự động bao gồm cả những dịch vụ mới khi bạn bắt đầu dùng tới, nên không cần cấu
hình lại mỗi lần bạn thử một thứ mới. Giới hạn là một AWS Service monitor và tối đa
500 custom monitor.

Alert subscription có ba tần suất. Thông báo DAILY và WEEKLY được gửi qua email; còn
IMMEDIATE được gửi qua SNS.

Có một điểm rất dễ hiểu nhầm: ngưỡng cảnh báo chỉ quyết định *khi nào một thông báo
được gửi đi*, chứ không quyết định thuật toán phát hiện hoạt động ra sao. Những bất
thường nằm dưới ngưỡng vẫn được ghi nhận và vẫn nhìn thấy được trong console - chúng
chỉ đơn giản là không kích hoạt cảnh báo.

### Tạo một budget

1. Mở AWS Billing and Cost Management Console rồi chọn **Budgets**, sau đó tạo một
   budget mới.
2. Chọn loại budget. Để theo dõi hóa đơn ở mức cơ bản thì cost budget là thứ bạn cần.
3. Đặt tên, chọn chu kỳ (thường là theo tháng), và nhập số tiền ngưỡng. Ở bước này
   bạn có thể thu hẹp budget về những dịch vụ cụ thể nếu chỉ muốn theo dõi EC2 hay
   RDS thay vì cả tài khoản.
4. Cấu hình cảnh báo. Nhập ngưỡng dưới dạng phần trăm hoặc một số tiền tuyệt đối,
   chọn mức chi thực tế hay dự báo, và nhập email nhận thông báo. Mình sẽ đặt vài
   mức, ví dụ 50, 80, và 100 phần trăm, để biết sớm chứ không phải đến khi đã vượt
   rồi mới hay.
5. Xem lại rồi tạo. Một email xác nhận sẽ được gửi tới từng địa chỉ bạn đã nhập, và
   phải xác nhận thì thông báo mới được gửi tới.

### Tạo một cost monitor

1. Vẫn trong console đó, chọn **Cost Anomaly Detection** rồi tạo một monitor mới.
2. Chọn loại monitor - AWS Services cho một tài khoản cá nhân.
3. Tạo một alert subscription, chọn tần suất và nhập một địa chỉ email hoặc một SNS
   topic.
4. Nhập ngưỡng cảnh báo, dưới dạng một số tiền tuyệt đối hoặc một tỉ lệ phần trăm cao
   hơn mức chi kỳ vọng.
5. Tạo monitor, rồi chờ. Một monitor mới có thể mất tới 24 giờ mới bắt đầu phát hiện,
   và mô hình cần khoảng 10 ngày dữ liệu chi tiêu lịch sử của một dịch vụ trước khi
   có thể phát hiện bất thường ở dịch vụ đó.

### Ưu điểm

* Bạn biết về một vấn đề chi phí trong vòng vài giờ tới một ngày, thay vì đến cuối
  tháng.
* Cảnh báo theo dự báo cho bạn thời gian hành động trước khi thật sự vượt ngưỡng.
* Cost Anomaly Detection không đòi hỏi một ngưỡng cho từng dịch vụ, bởi nó tự học thế
  nào là bình thường.
* Cảnh báo đi kèm phân tích nguyên nhân gốc rễ, chỉ ra dịch vụ hay loại mức sử dụng
  nào đã đẩy khoản chi bất thường lên.
* Budget Actions cho phép can thiệp tự động chứ không dừng ở việc thông báo.
* Việc theo dõi theo tag hoặc theo cost category giúp tách bạch chi tiêu theo từng dự
  án.

### Những điểm đáng biết

**Về chi phí.** Đây là phần mình thấy dễ hiểu sai nhất, bởi rất nhiều bài viết trên
mạng còn mang thông tin cũ. Theo trang giá hiện hành của AWS, việc theo dõi một budget
và nhận thông báo là **miễn phí**. Thứ bị tính tiền là Budget Actions: hai budget đầu
tiên có bật action mỗi tháng đều miễn phí, sau đó mỗi budget có bật action tốn 0,10
USD mỗi ngày. AWS Budgets Reports, tức các báo cáo theo lịch gửi qua email, tốn 0,01
USD cho mỗi báo cáo được gửi. Cost Anomaly Detection thì miễn phí, bao gồm cả việc tạo
monitor, phát hiện, và cảnh báo. Vậy nên với việc theo dõi hóa đơn cá nhân, gần như
không tốn gì cả.

**Về độ trễ.** Không công cụ nào chạy thời gian thực. Dữ liệu của Budgets làm mới
khoảng ba lần một ngày, còn Cost Anomaly Detection đánh giá mỗi ngày một lần. Điều này
nên được chấp nhận thay vì khó chịu: cả hai đều phát hiện vấn đề sớm hơn hẳn so với
ngồi chờ hóa đơn, nhưng không cái nào ngăn được khoản phí phát sinh ngay tại thời
điểm đó.

**Về tài khoản mới.** Vì mô hình cần dữ liệu lịch sử, Cost Anomaly Detection sẽ không
hiệu quả trên một tài khoản vừa mở hoặc một dịch vụ bạn vừa mới bắt đầu dùng. Trong
giai đoạn đầu đó, AWS Budgets với một ngưỡng đặt tay mới là thứ nên dựa vào.

**Về Budget Actions.** Hãy cân nhắc kỹ trước khi bật các hành động tự động. Việc tự
động dừng instance EC2 hay gắn một IAM policy có thể ảnh hưởng tới những tài nguyên
đang được sử dụng. Nếu tài khoản chỉ dùng để học, một cảnh báo qua email là đủ.

**Về việc đặt ngưỡng.** Ngưỡng đặt quá thấp sẽ sinh ra những thông báo bạn không cần,
và sau một thời gian bạn bắt đầu phớt lờ chúng. Đặt quá cao thì cảnh báo đến quá muộn
để còn có ý nghĩa.

### Khi nào nên dùng

* Dùng tài khoản cá nhân để học và muốn tránh những khoản phí bất ngờ.
* Cần chặn trần chi tiêu cho một môi trường cụ thể, chẳng hạn môi trường phát triển.
* Muốn phát hiện những tài nguyên bị bỏ quên trong trạng thái đang chạy.
* Cần tách bạch và theo dõi chi phí theo từng dự án hoặc từng nhóm.
* Muốn có can thiệp tự động khi chi tiêu vượt ngưỡng, chứ không chỉ nhận thông báo.

Với một tài khoản cá nhân, mình sẽ bắt đầu bằng một cost budget đơn giản cho toàn tài
khoản với vài mức cảnh báo, rồi bật Cost Anomaly Detection khi đã có vài tuần dữ liệu
chi tiêu.

### Kết luận

Hai công cụ trả lời hai câu hỏi khác nhau. AWS Budgets làm việc dựa trên một ngưỡng do
bạn đặt, nên nó hợp khi bạn đã biết mình muốn chi bao nhiêu. Cost Anomaly Detection
tự học thế nào là mức chi bình thường, nên nó bắt được những khoản tăng bất thường mà
bạn sẽ chẳng bao giờ nghĩ tới việc đặt ngưỡng cho chúng.

Điều mình thấy đáng nói nhất là phần giám sát và cảnh báo ở cả hai công cụ đều miễn
phí, vậy mà đó lại đúng là thứ hay bị bỏ qua nhất khi mới bắt đầu với AWS.

### Tài liệu tham khảo

* AWS Budgets - Managing your costs with AWS Budgets:
  https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html
* AWS Budgets Pricing:
  https://aws.amazon.com/aws-cost-management/aws-budgets/pricing/
* AWS Cost Anomaly Detection - Detecting unusual spend:
  https://docs.aws.amazon.com/cost-management/latest/userguide/manage-ad.html
* AWS Cost Anomaly Detection - FAQs:
  https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/faqs/
* AWS Cost Management API - AnomalySubscription:
  https://docs.aws.amazon.com/aws-cost-management/latest/APIReference/API_AnomalySubscription.html

<!-- Screenshot of the published post goes in
     static/images/3-BlogsPosted/3.1-Blog1/ and is referenced like:
-->
![Bài viết đã đăng trên trang cộng đồng AWS Study Group](/images/3-BlogsPosted/3.1-Blog1/post.png)
