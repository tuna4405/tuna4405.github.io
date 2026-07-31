---
title : "Thiết kế hệ thống"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Tổng quan

Trước khi bất kỳ ai trong hai lập trình viên viết một dòng code ứng dụng, nhóm đã
thống nhất và đóng băng hai tài liệu: bản đặc tả API và schema cơ sở dữ liệu.
"Đóng băng" ở đây mang nghĩa đen - không tài liệu nào được một người sửa mà không
có sự đồng ý của người kia, và mọi thay đổi về sau đều được ghi vào changelog
riêng của từng tài liệu chứ không bị sửa âm thầm tại chỗ. Mục này trình bày cả hai
bản hợp đồng ấy cùng với bản đồ màn hình nối chúng với những gì người dùng thực sự
nhìn thấy. Mục này không có thao tác nào trên AWS Console; mọi thứ ở đây là phần
thiết kế phải đúng trước khi mục 5.4 bắt đầu xây dựng dựa trên nó.


#### Nội dung

- [Đặc tả API](5.3.1-api-spec/)
- [Thiết kế cơ sở dữ liệu](5.3.2-database-schema/)
- [Bản đồ màn hình và luồng người dùng](5.3.3-screen-map/)
