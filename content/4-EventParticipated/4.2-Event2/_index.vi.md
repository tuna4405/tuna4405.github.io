---
title: "Sự kiện 2"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Báo cáo tổng hợp: FCAJ Agentic AI Build Week — Community Day

### Mục tiêu của sự kiện

- Giới thiệu các hệ thống do các đội xây dựng trong hackathon Agentic AI Build Week
- Cho thấy các mẫu (pattern) AI dạng agentic được ráp lại từ các dịch vụ AWS ra sao
  trong thực tế
- Chia sẻ trải nghiệm xây dựng một prototype end-to-end dưới hạn chót 24 giờ, bao
  gồm cả những gì đã đi chệch hướng
- Cho những người lần đầu tham gia một hình dung thực tế về việc một hackathon
  diễn ra thế nào

### Các đội trình bày

- **Plan V** — *Solution Architect Professional Native App*
  (Phạm Tiến Thuận Phát, Huỳnh Hoàng Long, Lê Minh Nghĩa, Trần Đại Vĩ, Nguyễn An)
- **Signal Scout** — *Nền tảng phát hiện tín hiệu doanh nghiệp*
  (Lê Tấn Lực, Đỗ Hoàng Hiếu, Triệu Quốc Hào, Nguyễn Duy Khiêm, Nguyễn Công Minh, Nguyễn Trần Minh Quân)
- **One Team** — *KFC Bot Agent*, đội vô địch AABW Hackathon
  (Anh Duy, Trần Đông, Đoàn Trung, Minh Việt, Anshul Roy)
- **3KA** — *S.H.E.P.H.E.R.D* và hành trình hackathon
  (Huỳnh An Khương, Nguyễn Quốc Huy, Ngô Quang Khôi, Hoàng Lê Thành Đức, Đặng Nguyễn Phước Lộc, Đặng Trường Hưng)

---

### Những điểm nổi bật

#### Plan V — Solution Architect Professional Native App

Bài toán được đóng khung bằng một đoạn hội thoại mà bất kỳ ai làm tư vấn cũng nhận
ra ngay: khách hàng yêu cầu một thiết kế hệ thống AI cho bộ tài liệu quy trình vận
hành chuẩn của họ, muốn có vào thứ Năm, rồi ngay sau đó lại muốn có ngay lập tức.
Trong khi ấy, solution architect phải bóc tách yêu cầu, phác kiến trúc ban đầu, tạo
sơ đồ, và ước lượng chi phí cloud.

Công cụ của đội xử lý từng bước trong số đó. Nó phân tích yêu cầu từ ngôn ngữ tự
nhiên lẫn tài liệu có cấu trúc, phác ra các phương án kiến trúc có tính đến
hybrid-cloud và bám theo chuẩn của công ty, sinh sơ đồ chỉnh sửa được bằng đúng bộ
icon kiến trúc chính thức của AWS, đưa ra ước lượng chi phí mang tính định hướng
cho region `ap-southeast-1`, và nêu rõ những giả định của chính nó cùng các lỗ hổng
nó phát hiện trong phần yêu cầu. Việc tinh chỉnh diễn ra qua một khung chat bên
cạnh, với custom instruction riêng cho từng dự án.

Phần so sánh trước-và-sau là phần rõ ràng nhất của bài trình bày:

| Trước | Sau |
|---|---|
| Đọc tài liệu yêu cầu từng dòng một, thủ công | Tải lên và trò chuyện tự nhiên — có danh mục yêu cầu trong vài phút |
| Lần nào cũng bắt đầu từ trang giấy trắng | Có sẵn một bản nháp có cơ sở để phản biện lại |
| Viết infrastructure as code thủ công | Infrastructure as code được sinh ra |
| Ước lượng chi phí bằng cảm tính phụ thuộc kinh nghiệm | Một ước lượng định hướng được tạo ra cùng lúc với kiến trúc |

Điều tôi thấy đáng chú ý là cách họ định vị đầu ra như *một bản nháp đầu tiên để
phản biện lại* chứ không phải một sản phẩm hoàn chỉnh. Công cụ được đặt vào vai trò
xóa bỏ trang giấy trắng, chứ không phải xóa bỏ người kiến trúc sư.

#### Signal Scout — phát hiện sớm thay đổi chiến lược của doanh nghiệp

Đội này xây một nền tảng phát hiện sớm các tín hiệu tái cấu trúc và thay đổi chiến
lược ở các công ty, hướng tới các đội chiến lược doanh nghiệp, quản trị rủi ro,
tình báo cạnh tranh, và quản lý khách hàng B2B.

Hệ thống thực sự là multi-agent. Một Lambda function đứng trước một AgentCore
runtime, nơi điều phối hai subagent: một **crawler subagent** thu thập bằng chứng
từ các nguồn bên ngoài, và một **analysis subagent** diễn giải chúng, có áp dụng
Bedrock Guardrails. Bộ nhớ ngắn hạn nằm ở AgentCore Memory, session ở S3, và kết
quả ở DynamoDB. Phía hướng tới người dùng chạy qua Route 53, Amplify, API Gateway,
WAF, và Cognito, cùng CloudWatch và CloudTrail cho khả năng quan sát, Secrets
Manager và IAM cho thông tin xác thực và quyền truy cập.

Các tuyên bố giá trị họ đưa ra rất có kỷ luật: phân tích minh bạch và kiểm chứng
được, mọi kết luận đều có bằng chứng chống lưng, và hỗ trợ ra quyết định *do con
người kiểm soát* một cách rõ ràng. Hệ thống được thiết kế để cung cấp thông tin cho
một quyết định Maintain, Adapt, hay Accelerate, chứ không phải để tự đưa ra quyết
định đó.

Phần tôi tâm đắc nhất là **slide chi phí** — một bảng bóc tách đầy đủ từng khoản
theo ba mức sử dụng tối thiểu, trung bình, và tối đa, phủ mọi dịch vụ kể cả các phụ
thuộc ngoài AWS:

| | Min | Mid | Max |
|---|---|---|---|
| Tổng các dịch vụ AWS | ~ $17 | ~ $35 | ~ $130 |
| Crawling bên thứ ba | ~$35 | ~$30 | ~$200 |
| Công cụ observability | $0–29 | $29 | $29 |
| **Tổng cộng** | **~ $81** | **~ $94** | **~ $359** |

Sau đó họ trình bày một kiến trúc đã chỉnh lại theo hướng tiết kiệm chi phí hơn —
cho thấy phần phân tích chi phí thực sự đã quay ngược lại tác động đến thiết kế,
chứ không phải được làm ra sau cùng cho đủ một slide theo yêu cầu.

#### One Team — KFC Bot Agent (đội vô địch)

Đội thắng cuộc mở đầu bằng một thất bại có thật trong ngành chứ không phải bằng ý
tưởng của chính họ: McDonald's đã dừng một thử nghiệm AI cho drive-thru sau khi thử
đặt món tự động ở hơn một trăm cửa hàng tại Mỹ. Cách họ đọc câu chuyện ấy rất chính
xác — bài học không phải là đặt món bằng AI là một ý tưởng tồi, mà là **đặt món là
một bài toán hệ thống**. Một agent đặt món phải xử lý món, số lượng, biến thể, quy
tắc voucher, trạng thái giỏ hàng, và lỗi, trong khi ngôn ngữ tự nhiên thì lộn xộn,
quy tắc kinh doanh thì nghiêm ngặt, đơn hàng phải được kiểm chứng, và sai sót thì
quy thẳng ra tiền.

Vấn đề họ nhắm tới là khoảnh khắc một thương hiệu đánh mất đơn hàng: khách đang
đói và ý định mua xuất hiện ngay giữa cuộc trò chuyện, nhưng việc đặt món lại buộc
họ chuyển sang ứng dụng khác, tạo tài khoản, và dò menu — thế là cái đà ấy biến
mất. Hỗ trợ chat thuần con người thì không mở rộng nổi qua nhiều kênh, nhiều ca
trực, và những đợt tăng đột biến lưu lượng.

Sản phẩm của họ là một agent đặt món hội thoại đa kênh chạy ngay bên trong Zalo và
Messenger, không phải chuyển ứng dụng, không phải tạo tài khoản mới, không phải giải
thích lại từ đầu.

Luận điểm kiến trúc họ đưa ra là điều tôi nghĩ tới nhiều nhất:

> **Một chatbot trả lời. Một agent hành động.**

Họ mô tả một vòng lặp năm bước — hiểu ý định đặt món, lập kế hoạch các bước cần
làm, gọi tool để tra cứu dữ liệu kinh doanh đáng tin cậy, hành động bằng cách cập
nhật giỏ hàng và áp khuyến mãi, rồi kiểm chứng lại với trạng thái giỏ hàng thật.
Câu tóm tắt của họ: *mô hình hiểu, còn tool mới quyết định cái gì là thật*. Mô hình
ngôn ngữ không được tin cậy để giữ trạng thái của đơn hàng; nó chỉ được tin cậy để
diễn giải yêu cầu.

#### 3KA — S.H.E.P.H.E.R.D và hành trình hackathon

Bài trình bày này được dựng như một câu chuyện chứ không phải một màn demo sản
phẩm, đi qua bốn chặng: đăng ký và chọn hướng thi, xây dựng dưới áp lực, ngày demo
và chấm giải, và những chiêm nghiệm sau đó.

Hệ thống S.H.E.P.H.E.R.D — Smart Human-flow Evaluation, Prediction, Hazard
Detection, Response, and Dispatch — phân tích hình ảnh camera trực tiếp tại các địa
điểm tổ chức để phát hiện và bám theo người, đo mật độ đám đông, ước lượng tình
trạng hàng chờ, nhận ra dấu hiệu sớm của ùn tắc, dự báo quá tải, phát cảnh báo chủ
động, và gợi ý hành động cho nhân viên. Nó được xây bằng YOLO và ByteTrack cho phần
phát hiện và bám theo, Amazon SageMaker, Amazon Bedrock AgentCore cùng Strands Agent
cho tầng agentic, và một dashboard React.

Tầng agentic có hai vai trò tách bạch: một **autonomous monitor** theo dõi các chỉ
số trực tiếp và phát cảnh báo mà không cần ai hỏi, và một **operator copilot** cho
phép nhân viên đặt câu hỏi bằng ngôn ngữ tự nhiên và nhận câu trả lời có chỉ số
trực tiếp cùng các công cụ dự báo chống lưng. Cách đóng khung vấn đề rất cụ thể —
nhân viên tại địa điểm phải cùng lúc trông chừng lối vào, hàng chờ, các gian hàng,
và dòng di chuyển, trong khi giám sát thủ công thì chậm, bị động, khó mở rộng, và
dễ bỏ sót sự cố.

Họ trung thực một cách hiếm thấy về trải nghiệm của mình. Những nỗi sợ trước ngày
đầu tiên được liệt kê thẳng thừng: chưa đủ giỏi, sợ thất bại, mù mờ không biết gì,
quá ít thời gian. Những thử thách lớn nhất của họ là không có nền tảng AI, lần đầu
làm việc với AWS, thời gian hạn hẹp, code không chạy, và thiếu ngủ. Mạch cảm xúc họ
kể lại đi từ choáng ngợp, qua lúc tìm được nhịp khi ý tưởng đã thông, đến niềm tự
hào vì đã thực sự làm ra được một thứ gì đó.

Lời khuyên của họ cho những người lần đầu tham gia:

- **Cứ đăng ký đi** — đừng đợi đến khi thấy mình đã sẵn sàng
- **Tìm đội sớm** — kỹ năng khác nhau tốt hơn kỹ năng giống hệt nhau
- **Thu phạm vi thật nhỏ** — một tính năng, làm cho tốt
- **Nói chuyện với tất cả mọi người** — mentor và các đội khác chính là lý do bạn có mặt ở đó

---

### Những điều rút ra

**Agent được định nghĩa bởi tool của nó, không phải bởi mô hình.** Đội nào cũng vạch
đúng một ranh giới ấy: mô hình ngôn ngữ diễn giải ý định, còn tool thực thi và kiểm
chứng hành động trên trạng thái thật. Cách diễn đạt của One Team — mô hình hiểu,
tool quyết định cái gì là thật — là câu phát biểu rõ ràng nhất về điều này mà tôi
từng nghe, và về bản chất đó là một lập luận về tính đúng đắn chứ không phải một
lập luận về AI.

**Chi phí thuộc về khâu thiết kế, không phải khâu sau đó.** Signal Scout đưa ra một
ước lượng bóc tách theo từng khoản ở ba mức sử dụng *rồi thiết kế lại* cho hiệu quả
hơn. Đó là một kỷ luật mà trước đây tôi vẫn xem là chuyện của vận hành chứ không
phải một đầu vào của thiết kế.

**Một bản nháp có cơ sở hơn hẳn một trang giấy trắng.** Việc Plan V định vị công cụ
của họ là thứ tạo ra cái để phản biện lại chứ không phải cái để chấp nhận là một mô
tả trung thực hơn về công dụng thật của những hệ thống này so với phần lớn tuyên bố
sản phẩm.

**Ràng buộc tạo ra phạm vi tốt hơn tham vọng.** "Thu phạm vi thật nhỏ — một tính
năng, làm cho tốt" đến từ một đội chỉ có 24 giờ; nó đúng không kém với một dự án
kéo dài bốn tuần.

---

### Áp dụng vào công việc

Ý tưởng chuyển giao được nhiều nhất là ranh giới kiểm chứng bằng tool. Trong dự án
của tôi, thứ tương đương là tầng ứng dụng đề xuất một booking, còn giao dịch cơ sở
dữ liệu mới quyết định nó có thật hay không — các dòng ghế bị khóa và kiểm tra lại
trước khi bất cứ thứ gì được commit, và mọi sự tự tin ở tầng ứng dụng cũng không
thể lấn át trạng thái trong cơ sở dữ liệu. Nghe bốn đội đi tới cùng một nguyên tắc
trong một lĩnh vực khác hẳn khiến tôi tự tin hơn rằng việc đặt cam kết ấy ở tầng dữ
liệu là lựa chọn đúng chứ không phải một cách làm gượng gạo.

Bảng chi phí của Signal Scout đã thay đổi cách tôi tiếp cận phần chi phí trong báo
cáo của mình. Thay vì khẳng định dự án nằm gọn trong Free Tier, tôi cấu trúc nó
thành từng khoản mục với những thành phần sẽ chiếm phần lớn hóa đơn được chỉ ra rõ
ràng — và chính điều đó mới làm cho ước lượng có ích với người đọc.

Việc region `ap-southeast-1` xuất hiện trong phần ước lượng chi phí của Plan V là
một chi tiết nhỏ nhưng đáng yên tâm: đúng lựa chọn region mà tôi đã đưa ra vì lý do
độ trễ, được một đội khác đưa ra một cách độc lập vì lý do chi phí.

Cuối cùng, danh sách nỗi sợ của 3KA — chưa đủ giỏi, mù mờ không biết gì, quá ít
thời gian — gần như đúng y những gì tôi cảm thấy lúc mới bắt đầu chương trình. Được
thấy một đội nói thẳng điều đó trên sân khấu, sau khi họ đã xây và trình diễn một hệ
thống chạy được, có lẽ là thứ hữu ích nhất tôi mang về từ ngày hôm đó.

<!-- Add photos to static/images/4-EventParticipated/4.2-Event2/ and reference them here, e.g.

-->
#### Ảnh tại sự kiện
![Tôi tại Agentic AI Build Week Community Day](/images/4-EventParticipated/4.2-Event2/selfie.jpg)
