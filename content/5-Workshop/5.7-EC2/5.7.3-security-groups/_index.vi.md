---
title : "Siết chặt Security Group"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.7.3 </b> "
---

Hai security group, được tạo ra từ trước cả khi những tài nguyên mà chúng bảo vệ
tồn tại (mục 5.5.1 và 5.7.2 đều đã nhắc tên `caerus-rds-sg` và `caerus-ec2-sg` từ
trước):

**`caerus-ec2-sg`** - instance ứng dụng:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| Custom TCP | 3000 | My IP |

**`caerus-rds-sg`** - tạo ra rỗng, rồi được thêm đúng một rule khi `caerus-ec2-sg`
đã tồn tại để tham chiếu tới:

| Type | Port | Source (trước) | Source (sau) |
|---|---|---|---|
| PostgreSQL | 5432 | *(không có rule - không thể tiếp cận)* | `caerus-ec2-sg` |

Trường **Source** của rule chính là security group của EC2, được chọn theo tên ngay
trong console, chứ không phải một khối CIDR hay một địa chỉ IP. Đây là điểm đáng nói
thẳng ra: RDS cho qua traffic từ *bất kỳ instance nào đang mang security group đó*,
bất kể hôm nay instance ấy tình cờ có địa chỉ IP nào. Một rule dựa trên IP sẽ phải
cập nhật mỗi lần instance bị dừng rồi khởi động lại, hoặc sẽ phải nới rộng tới mức
một dải IP vô nghĩa; còn một rule tham chiếu security group thì không bao giờ phải
cập nhật, và luôn chặt đúng ở mức "chỉ ứng dụng này mới chạm được tới cơ sở dữ liệu
này" chừng nào hai group đó còn tồn tại.

{{% notice note %}}
Nếu có lúc nào mất quyền truy cập SSH sau khi địa chỉ IP ở nhà thay đổi, cách sửa
nằm ngay trong khung này: sửa Source của rule SSH về lại "My IP" để console phân
giải lại địa chỉ hiện tại. Bản thân rule này chỉ là tạm thời - mục 5.7.7 sẽ gỡ bỏ nó
hoàn toàn khi các instance chuyển sang private subnet và chuyển sang dùng Systems
Manager Session Manager để quản trị.
{{% /notice %}}

<!-- ![caerus-rds-sg inbound rules, before (empty) and after (5432 from caerus-ec2-sg)](/images/5-Workshop/5.7-EC2/5.7.3-security-groups/example.png) -->
