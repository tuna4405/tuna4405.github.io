---
title: "Nhật ký công việc"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Nhật ký công việc này ghi lại toàn bộ kỳ thực tập kéo dài từ 15/06/2026 đến 31/07/2026, được ghi theo từng tuần, tất cả là bảy tuần.

Dự án - **Caerus**, nền tảng đặt vé xem phim theo chỗ ngồi do một nhóm hai người thực hiện - đã được chốt ngay trong Tuần 1 thay vì để lại sau: bản đặc tả API (API specification) và schema cơ sở dữ liệu được thống nhất rồi "đóng băng" (frozen) trước khi nhóm đi sâu vào bất kỳ dịch vụ AWS nào. Tuần 2 và Tuần 3 đi qua những kiến thức nền tảng về AWS cần có để xây dựng và triển khai (deploy) hệ thống. Tuần 4 dựng ứng dụng ở môi trường local, cố ý để lại hai endpoint chỉ thực hiện được khi đã có hạ tầng AWS thật. Tuần 5 đưa hệ thống lên AWS và gỡ bỏ hai điểm lỗi đơn (single point of failure) của nó - tầng compute và cơ sở dữ liệu. Tuần 6 làm nốt các endpoint phụ thuộc AWS, đặt toàn bộ ứng dụng sau một tên miền HTTPS duy nhất, kéo tầng compute ra khỏi internet công cộng hoàn toàn, và dựng hệ thống giám sát (monitoring). Tuần 7 chứng minh cam kết cốt lõi của hệ thống ngay trên môi trường đã triển khai và kết thúc bằng bản báo cáo viết.

Với mỗi tuần bên dưới đều có: các mục tiêu đặt ra lúc đầu tuần, những công việc đã làm theo từng ngày, và kết quả thực tế thu được.

**Tuần 1:** [Nhập môn và xác định phạm vi dự án](1.1-Week1/)

**Tuần 2:** [Nền móng tài khoản và quản lý danh tính, truy cập (Identity and Access Management)](1.2-Week2/)

**Tuần 3:** [Compute, lưu trữ, và các dịch vụ cơ sở dữ liệu quản lý (managed database)](1.3-Week3/)

**Tuần 4:** [Caerus - các endpoint cốt lõi, chỉ trên localhost](1.4-Week4/)

**Tuần 5:** [Caerus - đưa lên AWS](1.5-Week5/)

**Tuần 6:** [Caerus - các tính năng dựa trên AWS, CDN, tăng cường bảo mật mạng, và khả năng quan sát](1.6-Week6/)

**Tuần 7:** [Caerus - xác minh, báo cáo, và nộp bài](1.7-Week7/)
