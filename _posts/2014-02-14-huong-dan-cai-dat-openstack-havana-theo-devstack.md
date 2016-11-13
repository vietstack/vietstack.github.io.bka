---
title: Hướng dẫn cài đặt OpenStack Havana theo devstack
comments: true
categories: 
  - Tech
tags:
  - Chia sẻ
  - VietStack
  - Kinh nghiệm
  - devstack
---
Hướng dẫn cài đặt Openstack - Hanana theo kịch bản của devstack, áp dụng với mô hình All In One (tất cả các thành phần của Openstack trong 1 máy chủ).

Giới thiệu:<!--more-->
Chuẩn bị: Dựng máy ảo Ubuntu để cài đặt Openstack
Bước 1: Kiểm tra cấu hình Ubuntu trước khi cài
Bước 2: Tạo tài khoản stack và phân quyền
Bước 3: Tải devstack từ github
Bước 4: Khai báo các thông số cho file localrc để cài đặt openstack
Bước 5: Thực thi file stack.sh
Bước 6: Truy cập vào web và đăng nhập để dùng thử

Bước 1: Cài đặt ubuntu server 12.04 trên vmware workstation, đặt IP và kiểm tra kết nối ra internet.

Bước 2: Tạo tài khoản stack và phân quyền
- Tạo tài khoản stack:
$ adduser stack

- Cấp quyền cho user stack để khi thực thi sudo không cần hỏi mật khẩu.
$ echo "stack ALL=(ALL) NOPASSWD: ALL" &gt;&gt; /etc/sudoers

Bước 3: Chuyển sang tài khoản root, tải gói git và tải devstack từ github.
- Từ tài khoản root chuyển sang tài khoản stack bằng lệnh
$ su - stack

- Tải gói git bằng lệnh:
$ sudo apt-get install git -y

- Tải devstack từ github bằng lệnh:
git clone <a href="https://www.facebook.com/l.php?u=https%3A%2F%2Fgithub.com%2Fopenstack-dev%2Fdevstack.git&amp;h=qAQF8cv-w&amp;enc=AZNXGeBl-Ng8CFMuuq-mh-myf1LSHSwxHEpXs0U4WG34zX2zks9QPHqrHaz1q50D6bnOR2y-brzYXrG5YNzZU5F8qMlkF8kv38fg7rYiwww-y0TQ1lmsvbWH_aw2ZweOaDNH5EaLmUyEeU40RrItgphGl1y2OlC-EZJOMdDqI5JwwQ&amp;s=1" target="_blank" rel="nofollow">https://github.com/openstack-dev/devstack.git</a>

- Di chuyển vào thư mục devstack
cd devstack

Bước 4: Copy file localrc từ /devstack/samples/localrc ra thư mục hiện tại và sửa đổi file localrc cho phù hợp.

- Copy file localrc bằng lệnh
$ cp samples/localrc localrc

- Mở file localrc vừa tạo bằng vi hoặc công cụ mà bạn quen dùng.
$ vi localrc

- Sửa file localrc: xóa hoặc comment từ dòng 26 đến dòng 29 và thêm nội dung dưới vào

# KHAI BAO CAC THONG SO
HOST_IP=192.168.1.21
FLOATING_RANGE=192.168.10.0/24
FIXED_RANGE=192.168.70.0/24
FIXED_NETWORK_SIZE=256
FLAT_INTERFACE=eth0
ADMIN_PASSWORD=passw0rd
MYSQL_PASSWORD=passw0rd
RABBIT_PASSWORD=passw0rd
SERVICE_PASSWORD=passw0rd
SERVICE_TOKEN=admintoken

Bước 5: Sau khi sửa xong, lưu file localrc lại và thực thi file stack.sh
$ ./stack.sh

Bước 6:
Chờ đợi quá trình cài đặt, file stack.sh sẽ thực hiện các bước cần thiết thể tải các gói cho openstack.

Bước 7: Nhào vô vào dashboard để dùng thử openstack.
http://Ip_may_ubuntuserver/

Chú ý: các thông số về IP cần áp dụng đúng với mô hình mạng của bạn. Chi tiết hơn xem tại devstack.org

Link down tài liệu:
<a href="https://dl.dropboxusercontent.com/u/76793585/Huong%20dan%20cai%20dat%20Openstack%20-%20havana%20theo%20devstack_v1.pdf" target="_blank" rel="nofollow">https://dl.dropboxusercontent.com/u/76793585/Huong%20dan%20cai%20dat%20Openstack%20-%20havana%20theo%20devstack_v1.pdf</a>
