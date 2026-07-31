---
title: "Nhật ký công việc"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Nhật ký công việc này bao quát toàn bộ kỳ thực tập từ 15/06/2026 đến 31/07/2026, được ghi lại theo từng tuần, tổng cộng bảy tuần.

Dự án - **Caerus**, một nền tảng đặt vé xem phim theo chỗ ngồi do một nhóm hai người xây dựng - được chọn ngay từ Tuần 1 chứ không để đến sau: bản đặc tả API (API specification) và schema cơ sở dữ liệu được thống nhất và "đóng băng" (frozen) trước khi bất kỳ dịch vụ AWS nào được tìm hiểu sâu. Tuần 2 và Tuần 3 trình bày các kiến thức nền tảng AWS cần thiết để xây dựng và triển khai (deploy) hệ thống. Tuần 4 xây dựng ứng dụng ở môi trường local, chủ động bỏ qua hai endpoint cần có hạ tầng AWS thật mới thực hiện được. Tuần 5 triển khai hệ thống lên AWS và loại bỏ hai điểm lỗi đơn (single point of failure) của nó - tầng compute và cơ sở dữ liệu. Tuần 6 hoàn thiện các endpoint phụ thuộc AWS, đưa toàn bộ ứng dụng ra sau một tên miền HTTPS duy nhất, đưa tầng compute ra hoàn toàn khỏi internet công cộng, và thiết lập hệ thống giám sát (monitoring). Tuần 7 chứng minh cam kết cốt lõi của hệ thống trên chính môi trường đã triển khai và khép lại bằng báo cáo viết.

Mỗi tuần dưới đây liệt kê các mục tiêu đề ra vào đầu tuần, các công việc thực hiện theo từng ngày, và những gì thực sự đã đạt được.

**Tuần 1:** [Định hướng và xác định dự án](1.1-Week1/)

**Tuần 2:** [Nền tảng tài khoản và quản lý danh tính, truy cập (Identity and Access Management)](1.2-Week2/)

**Tuần 3:** [Compute, lưu trữ, và cơ sở dữ liệu quản lý (managed database)](1.3-Week3/)

**Tuần 4:** [Caerus - các endpoint cốt lõi, chỉ chạy local](1.4-Week4/)

**Tuần 5:** [Caerus - triển khai lên AWS](1.5-Week5/)

**Tuần 6:** [Caerus - các tính năng phụ thuộc AWS, CDN, tăng cường bảo mật mạng, và giám sát](1.6-Week6/)

**Tuần 7:** [Caerus - kiểm thử, báo cáo, và nộp bài](1.7-Week7/)
