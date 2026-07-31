---
title : "Thiết kế hệ thống"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Tổng quan

Trước khi cả hai developer viết bất kỳ dòng code ứng dụng nào, nhóm đã thống
nhất và "đóng băng" hai tài liệu: đặc tả API và schema cơ sở dữ liệu. "Đóng
băng" ở đây có nghĩa đen là như vậy - không tài liệu nào có thể bị một người
thay đổi mà không có sự đồng ý của người kia, và mọi thay đổi về sau đều
được ghi lại trong changelog riêng của từng tài liệu thay vì được chỉnh sửa
âm thầm tại chỗ. Mục này bao gồm cả hai bản hợp đồng (contract) đó cùng với
bản đồ màn hình liên kết chúng với những gì người dùng thực sự nhìn thấy.
Không có thao tác nào trên AWS Console trong mục này; mọi thứ ở đây là
thiết kế cần phải đúng trước khi mục 5.4 bắt đầu xây dựng dựa trên nó.


#### Nội dung

- [Đặc tả API](5.3.1-api-spec/)
- [Thiết kế cơ sở dữ liệu](5.3.2-database-schema/)
- [Bản đồ màn hình và luồng người dùng](5.3.3-screen-map/)
