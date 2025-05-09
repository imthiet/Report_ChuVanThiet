# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 5b Mục 19-21)

## Mục lục
### Haproxy
  

- [1 Tổng quan về giải pháp HAProxy ](#1-tổng-quan-về-giải-pháp-haproxy)

- [2 Tìm hiểu file cấu hình HAproxy](#2-tìm-hiểu-file-cấu-hình-haproxy)

- [3 Cài đặt, triển khai HAProxy và Keepalived cho Apache trên Ubuntu 22.04](#3-cài-đặt-và-triển-khai-haproxy-và-keepalived-cho-apache-trên-ubuntu-2204)



### 1 Tổng quan về giải pháp HAProxy
  - HAProxy hay High Availability Proxy là một phần mềm mã nguồn mở dùng đề cân bằng tải và chuyển tiếp Proxy cho các ứng dụng mạng, chủ yếu là các giao thức TCP và HTTP.
  
  - Nó được thiết kế để cung cấp hiệu suất cao, độ tin cậy và khả năng mở rộng cho các hệ thống phân tán, đặc biệt trong các môi trường web hoặc ứng dụng yêu cầu khả năng chịu tải lớn.

1.1 Các thuật ngữ cơ bản trong HAProxy:

  -Frontend - Nơi tiếp nhận kết nối từ client. Lắng nghe các cổng hoặc địa chỉ IP.

  -Backend - Tập hợp các server xử lý request từ frontend.

  -ACL( Access Control List) - Luật điều kiện cho phép lọc, định tuyến request.

  -Proxy - Một dịch vụ trung gian giữa client và server backend.

  -Sticky Session - Gắn kết 1 client với 1 backend cố định( duy trì session).

  -Health Check - Kiểm tra tình trạng backend để loại bỏ server lỗi.


1.2 Các kiểu Load Balancing

  - Round Robin( xoay vòng): Phân phối đều luotj request
  - Least Connection: Gửi request đến backend có ít kết nối nhất
  - Source: Dựa trên IP client để giữ sticky session


### 2 Tìm hiểu file cấu hình HAProxy

- `( /etc/haproxy/haproxy.cfg)`

```
global
    log /dev/log local0
    maxconn 2000
    user haproxy
    group haproxy

defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

frontend http_front
    bind *:80
    default_backend apache_backend

backend apache_backend
    balance roundrobin
    option httpchk
    server web1 192.168.56.101:80 check
    server web2 192.168.56.102:80 check

```
- Giải thích thêm về file cấu hình:

  -Global: đây là phần cấu hình toàn cục của  HAProxy

    daemon: Chạy HAProxy dưới dạng tiến trình nền

  -Defaults: có chế độ http, cấu hình log, set timeout,... mặc định
  
  -Frontend: Cấu hình để tiếp nhận các request từ client, chuyển tueeps request đến backend
  
  -BAckend: Tập hợp các server để nhạn request từ front end
    Cân bằng tải với round robin, kiểm tra healthcheck
  
### 3 Cài đặt và triển khai HAProxy và Keepalived cho Apache trên Ubuntu 22.04



Bước 1: Cài đặt Apache & HAProxy trên các node( 2 máy ảo) 

```
sudo apt update
sudo apt install apache2 haproxy -y

```

![image](https://github.com/user-attachments/assets/96cf4d6a-5ad7-4064-a74a-d69f8748ff48)


Bước 2: Cấu hình HAProxy như phần 2 ở trên:

Dùng lệnh `sudo  nano /etc/haproxy/haproxy.cfg`



Config file:

![image](https://github.com/user-attachments/assets/0fc65fd9-eaf4-4a1c-bb7c-2cb82e05dc58)


Bước 3: Cài đặt và cấu hình Keepalived cho HA (2 node HAProxy)

`sudo apt install keepalived -y
`
![image](https://github.com/user-attachments/assets/f20d30b3-b5ec-4437-878b-76b7a35d28d1)


- Cấu hình Keepalived :  `/etc/keepalived/keepalived.conf` - máy Master

` sudo nano /etc/keepalived/keepalived.conf`

  
```
vrrp_instance VI_1 {
    state MASTER
    interface ens33
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass secret123
    }
    virtual_ipaddress {
        192.168.80.200
    }
}
```


- Cấu hình Keepalived :  `/etc/keepalived/keepalived.conf` - máy backup

```
vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass secret123
    }
    virtual_ipaddress {
        192.168.80.200
    }
}
```

- sau đó khỏi động lại dịch vụ:

```
sudo systemctl restart keepalived
sudo systemctl enable keepalived

```


![image](https://github.com/user-attachments/assets/d941dfc2-2360-42fc-a153-1bdba20bfc9d)




Bước 3: Kiểm tra hoạt động:

- Kiểm tra VIP đang nằm ở đâu với lệnh trên cả backup và master

`ip a | grep 192.168.80.200`

Máy master:

![image](https://github.com/user-attachments/assets/401983ab-5cc8-4f40-a322-f87ffbdb76b6)

Máy backup:

![image](https://github.com/user-attachments/assets/8fbb8f6f-17fa-4d2e-95c6-87e8f78d9c9d)


-> Máy master đang giữ VIP nên mọi thứ đúng. vì master đang giữ nên backup không có ip này

- Tiếp đến ta tắt dịch vụ hoặc shut down máy Master:

Tắt dịch vụ keepalived: `sudo systemctl stop keepalived`

![image](https://github.com/user-attachments/assets/fbf019a8-2442-4340-9dba-9a1b52b2ca06)


Kiểm tra lại trên máy backup:

Đã có VIP-> Thành Công!!!:

![image](https://github.com/user-attachments/assets/0cf9a512-e030-4aa6-915c-6b32321cd9b6)



- Khởi động lại Master để VIP tự động quay lại master vì: Priority cao hơn và 2 máy hoạt động bình thường

  ![image](https://github.com/user-attachments/assets/82bcb71f-e1a5-4389-9b0a-b47d07ee86a6)

->VIP đã quay trở lại Master

  ![image](https://github.com/user-attachments/assets/66569d76-fddf-4dd3-823a-b3e5f66fd7f0)

====> Thành Công triển khai!!!!











