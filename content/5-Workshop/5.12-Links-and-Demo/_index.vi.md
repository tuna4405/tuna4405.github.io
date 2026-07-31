---
title : "Repository, trang web, và video demo"
date : 2026-06-01
weight : 12
chapter : false
pre : " <b> 5.12 </b> "
---

Mọi thứ được mô tả trong các phần trên đều quy về ba artifact cụ thể, có
thể kiểm chứng được: mã nguồn, hệ thống đang chạy, và một bản ghi hình cho
thấy nó hoạt động. Cả ba đều được liên kết ở đây thay vì rải rác khắp báo
cáo.

#### Mã nguồn

<!-- ĐIỀN VÀO: URL của GitHub repository, ví dụ https://github.com/<org>/caerus-booking -->

Repository chứa cả hai package `backend` và `frontend`, các file SQL
migration và seed được nhắc đến trong [Amazon RDS](/5-Workshop/5.5-RDS/), và
thư mục `docs/` chứa đặc tả API và database schema đã được chốt (frozen) từ
[System Design](/5-Workshop/5.3-Design/).

#### Ứng dụng đang chạy

**[https://d2xqaej6i413ey.cloudfront.net](https://d2xqaej6i413ey.cloudfront.net)**

Đây là domain của CloudFront distribution được mô tả trong
[CloudFront: Một domain HTTPS duy nhất cho tất cả](/5-Workshop/5.7-EC2/5.7.6-cloudfront/)
- cùng một domain này phục vụ cả frontend React lẫn, dưới đường dẫn
`/api/*`, Express API đứng sau load balancer. Có sẵn một tài khoản demo để
đăng nhập (xem phần phụ lục của báo cáo để lấy thông tin đăng nhập), hoặc
một tài khoản admin để tạo screening và tải lên poster.

{{% notice note %}}
Link này chỉ hoạt động khi hạ tầng bên dưới vẫn đang chạy. Nếu được xem xét
sau khi [Dọn dẹp tài nguyên](/5-Workshop/5.11-Cleanup/) đã được thực hiện,
thì video demo bên dưới chính là bằng chứng ghi lại hệ thống đã hoạt động.
{{% /notice %}}

#### Video demo

<!-- ĐIỀN VÀO: link đến video walkthrough đã ghi hình, ví dụ một link YouTube/Drive -->

Được ghi hình từ đầu đến cuối trên môi trường đã triển khai, không phải môi
trường local: đăng nhập, duyệt danh sách screening, sơ đồ ghế, một lượt đặt
vé thông thường, bài kiểm thử concurrency từ [Kiểm thử](/5-Workshop/5.9-Testing/)
chạy trên hai phiên trình duyệt, hủy vé, và tải về vé PDF kết quả.

<!-- ![Screenshot trang chủ của ứng dụng đang chạy](/images/5-Workshop/5.12-Links-and-Demo/example.png) -->
