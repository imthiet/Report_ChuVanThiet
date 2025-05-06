
# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 2 Mục 13-)

## Mục lục

- [1 DNS](#1-dns)
  - [1.1 Tìm hiểu về hệ thống DNS](#11-hệ-thống-dns)
  - [1.2 Các loại bản ghi sử dụng trong DNS](#12-các-loại-bản-ghi-trong-dns)
 
- [2 Công Cụ Làm Việc Trong Quá Trình Thực Tập](#2-công-cụ-làm-việc-trong-quá-trình-thực-tập)
  - [2.1 Chat trao đổi](#21-công-cụ-trao-đổi)
  - [2.2 GitHub](#22-github)

## 1 DNS

### 1.1 Hệ Thống DNS

  - DNS viết tắt của Domain Name System. Là phần xử lý ở giữa IP của các thiết bị máy chủ và tên miền trên Internet.
  - Về Tổng Quan: Client( Người dùng) gửi request tên miền lên Internet qua web browser, DNS tiếp nhận, phân giải tên miền và tìm địa chỉ IP của web server và trả về cho CLient. Client gửi request HTTP/HTTPs(get,post,update,...) đến webserver nhờ ip vừa nhận được. Webserver nhận được request đó và trả về response cho CLient.
  - Sơ đồ tổng quan:



![image](https://github.com/user-attachments/assets/68428d1b-6d27-4a86-a994-ecd6379a6916)

  - Đi sâu hơn vào DNS.

![image](https://github.com/user-attachments/assets/431cc02e-7bd6-4478-8368-facf1bdae775)


  - Có 2 loại DNS:
    - Public DNS: là loại phổ biến hơn cho mọi người có thể sử dụng để truy cập Internet và dược cung cấp bởi các công ty lớn như Google,OpenDNS,...
    - Private DNS: loại ít phổ biến hơn dùng cho các doanh nghiệp, tổ chức cho mạng nội bộ. Có tính bảo mật và tùy chỉnh cao hơn. 
### 1.2 Các loại bản ghi trong DNS
  - Có nhiều loại bản ghi trong DNS, mỗi loại đều có chức năng riêng biệt.
    - A: bản ghi trỏ tên miền đến địa chỉ IPv4 (abc.com -> 192.168.80.128)
    - AAAA: bản ghi trỏ tên miền đến địa chỉ IPv6 (abc.com -> 2606:2800:220:1:248:1893:25c8:1946)
    - CNAME(Canonical Name) : Trỏ tên miền đến 1 tên miền khác(alias) (abc.com -> xyz.com)
    - MX(Mail Exchange): Chỉ định máy chủ mail cho tên miền
    - NS(Name Server): Chỉ định các DNS server có thẩm quyền cho tên miền
    - Ngoài ra còn TXT,PTR,SOA,SRV,CAA,... 

