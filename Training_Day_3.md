# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 3 Mục 16-17)

## Mục lục
- [Cài đặt Linux Live Server thay cho bản desktop hiện tại](#cài-đặt-live-server-linux)
- [1 Triển khai ứng dụng mail server Zimbra](#1-triển-khai-ứng-dụng-mail-server-zimbra)
  - [1.1 Cài đặt email server zimbra trên Ubuntu 22.04](#11-cài-đặt-email-server-zimbra-trên-ubuntu-2204)
  - [1.2 Khởi tạo user zimbra](#12-khởi-tạo-user-zimbra)
  - [1.3 Thiết lập chính sách về mật khẩu account email](#13-khởi-tạo-user-zimbra)
  - [1.4 Thiết lập chữ ký email Zimbra](#14-thiết-lập-chữ-ký-email-zimbra)
  - [1.5 Thiết lập forward email Zimbra](#15-thiết-lập-forward-email-Zimbra)


- [2 Triển khai ứng dụng mail server MDaemon(trial)](#2-triển-khai-ứng-dụng-mail-server-mdaemon)
  - [2.1 Cài đặt Email Server Mdaemon trên Windows server(2019-2022)](#21-cài-đặt-email-server-mdaemon-trên-windows-server)
    
  - [2.2 Tray Cập Admin và EndUser](#22-truy-cập-admin-và-enduser)
    


## Cài đặt Live Server Linux
- Cài đặt Ubuntu Server bản 22.04.5 thay thế cho bản desktop trước đó.
- Cấu hình Hardware, Software,...  

  ![image](https://github.com/user-attachments/assets/ef4bcf7f-5ed9-444e-8bd3-b21e01e92a95)

- Giao diện dòng lệnh:

![image](https://github.com/user-attachments/assets/ad33d8f2-52bc-4758-95d1-a1440ac2e478)



          
## 1 Triển khai ứng dụng mail server Zimbra

- Tổng Quan: Email server Zimbra sẽ biến máy chủ Linux thành 1 hệ thống gửi/nhận email hoản chỉnh(SMTP, IMAP, POP3, Webmail,v.v).
- Zimbra yêu cầu cấu hình server khá cao: Ram 8GB, CPU > 2 core, Ổ cứng > 20GB.
### 1.1 Cài đặt email server Zimbra trên Ubuntu 22.04
B1: Cập nhật hệ thống với lệnh quen thuộc:

```
sudo apt update && sudo apt upgrade -y
sudo reboot
```
 -> đợi tự động update và reboot lại hệ thống

 
 ![image](https://github.com/user-attachments/assets/36aa7d5f-ee6e-46d2-9791-d0fd77e6cc5d)
 

B2: Cấu hình HostName & /etc/hots
- Đặt hostname(phần này quan trọng vì ảnh hướng đến các phần sau)

  `sudo hostnamectl set-hostname mail.tqmail.com`

  ![image](https://github.com/user-attachments/assets/82f79cf4-ba44-449b-aa30-2ac2472c997b)


- Dùng `Nano` để chỉnh sửa /etc/hosts:
  
            `sudo nano /etc/hosts`
  
- Thêm dòng này vào /etc/hots:
  
            `127.0.0.1 mail.tqmail.com mail`

  ![image](https://github.com/user-attachments/assets/c86c081d-c5d0-4911-a4c7-2d99b9caafe2)

- Sau đó CRT+x -> Yes -> Enter
- Tiếp theo ta cài các gói cần thiết khác:

```

sudo apt update
sudo apt install -y net-tools curl perl unzip pax sysstat sudo
```

- Thống báo như dưới là ok( tất cả đều ở bản Newest version)

![image](https://github.com/user-attachments/assets/122bc0c3-43c3-4dd3-be43-1c4053b6fa7d)

B3: Tải bộ cài Zimbra(Open Source)
-  Đầu tiên ta vào thư mục /opt và tải bộ cài. Vì là bản Ubuntu 22.04.5 nên chỉ phù hợp với Zimbra 10.1( dù chỉ là dạng beta)

```
cd /opt
sudo apt-get install nano wget bind9 bind9utils telnet perl ufw tar resolvconf net-tools -y

```
- Nhập password -> enter


![image](https://github.com/user-attachments/assets/c9a22580-e6c3-43b7-9f9f-c1c5d3871877)


- Set timezone, ở đây là Asis/Ho_Chi_Minh (UTC +7)
- Trong trường hợp có sử dụng Postfix, cần disable để tránh xung đột

```
systemctl stop postfix
systemctl disable postfix
```
- Config resolv.conf

```
systemctl enable resolvconf
systemctl start resolvconf
cp /etc/resolv.conf /etc/resolv.conf.conf.backup
echo "search saad.my.id" >> /etc/resolvconf/resolv.conf.d/head
echo "nameserver 139.162.17.31" >> /etc/resolvconf/resolv.conf.d/head
sudo resolvconf --enable-updates
sudo resolvconf -u

```
B4: Create a Local DNS Server
- Tạo zone trên DNS server bind

  
```
cp /etc/bind/named.conf.local /etc/bind/named.conf.local.backup
cp /etc/bind/named.conf.options /etc/bind/named.conf.options.backup
> /etc/bind/named.conf.local
sed -i '/directory*/a\        forwarders {8.8.8.8; 8.8.4.4;};' /etc/bind/tqmail.conf.options;
```

- Do Không thể tiếp tục làm trên Ubuntu liveServer được nên chuyển qua lại phiên bản Ubuntu 22.04 Desktop để cài. các bước tương tự đến B4.

B5: Sửa file /etc/bind/named.conf.local

        ```
        nano /etc/bind/named.conf.local
        ```  
Code: 
        
           ```zone  saad.my.id {
          
                  type master;
                  
                          file "/var/lib/bind/saad.my.id.hosts";
                          
                  allow-transfer {
                  
                          127.0.0.1;
                          localnets;
                          };
                  };
        ```

        
![image](https://github.com/user-attachments/assets/f642bf05-dbcc-443c-898e-08786e766ef6)



B6: Tạo file tqmail.com.hosts

- Tạo file thông qua Vi:

`vi /var/lib/bind/tqmail.hosts
`

Sử dụng code cho tqmail.hots:

```
$ttl 3600
@      IN      SOA     mail.tqmail.com root.mail.tqmail.com (
                        1615364925
                        3600
                        600
                        1209600
                        3600 )
tqmail.com       IN      NS     mail.tqmail.com
mail.tqmail.com  IN      A       139.162.17.31
tqmail.com       IN      MX      10 mail

```


![image](https://github.com/user-attachments/assets/010452ae-198c-49f7-b8b0-c13407d3ebb1)


B7: Restart và Enable DNS Server binding:

Code: 

  ```
    systemctl restart named

    systemctl enable named
```

![image](https://github.com/user-attachments/assets/fe1c0b17-877a-4592-a526-db0d56494db4)


B8: Kiểm tra lại COnfig với    `nslookup mail.tqmail.com`
-> hiển thị ra thông tin name, address là đã config thành công!


![image](https://github.com/user-attachments/assets/01a221db-9383-4613-aba0-20d19a99d95c)


B9: Tải và cài đặt Zimbra Mail Server

- Trước tiên để có thể tạo 1 server email với Zimbra, ta cần tải và cài đặt phần mềm Zimbra .
- Tải Zimbra mail server pakage( chạy lệnh và đợi khoảng 1phut):

```

cd /opt/

wget https://github.com/maldua/zimbra-foss-builder/releases/download/zimbra-foss-build-ubuntu-22.04/10.1.4/zcs-10.1.4_GA_4200000.UBUNTU22_64.20241224171952.tgz

```

![image](https://github.com/user-attachments/assets/9d999826-7ce5-4e18-a29d-9cd7fb743395)


- Sau đó giải nén Pakage vừa tải:

```
tar -zxvf zcs-10.1.4_GA_4200000.UBUNTU22_64.20241224171952.tgz
cd zcs-10.1.4_GA_4200000.UBUNTU22_64.20241224171952

```

- Sau khi giải nén sẽ thấy như sau:

  
![image](https://github.com/user-attachments/assets/6c88888d-a0ed-4621-bcf4-9c6d83e9af01)


- Tiếp tục chạy lệnh dưới để cài đặt Zimbra Mail Server:
  
`./install.sh`

-> Nhấn Y để đồng ý điều khoản, nhấn tiếp Y để chấp nhận sử dụng ZImbra's package repository


![image](https://github.com/user-attachments/assets/0f8b76d5-8420-407d-b4bc-b98edb260dae)


- Tiếp theo là các option cài đặt, nhấn y để cài hoặc n để từ chối:

![image](https://github.com/user-attachments/assets/05babc27-45a7-4bd7-8871-b6114cf3c2ce)

- Sau đó nhấn Y tiếp để modified. và đợi. Quá trình này có thể mất 10-20 phút.
- SAu khi cài xong, ta cần nhập Domain Name( lưu ý bỏ mail. phía trước tên miền)


.... Gặp Lỗi và Đang trong quá trình Fix!!!


### 1.2 Khởi tạo User Zimbra
### 1.3 Thiết lập chính sách về mật khẩu account email
### 1.4 Thiết lập chữ ký Zimbra email
### 1.5 Thiết lập forward email Zimbra

## 2 Triển khai ứng dụng mail server MDaemon
### 2.1 Cài đặt Email Server Mdaemon trên Windows server

B1: Tải file Iso của windows Server từ trang web chính thức của Microsoft:
https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022

B2: Thiết lập nó lên VMWare làm máy ảo( lưu ý cấu hình cho máy ảo ram ít nhất 2GB, Bộ nhớ 40GB)

![image](https://github.com/user-attachments/assets/0d3e6abe-008e-4395-bb17-b6b6ab49fdb5)

B3: Tải và cài đặt MDaemon Mail Server bản Trial  từ trang chính thức:

https://www.altn.com/Downloads/MDaemon-Mail-Server-Free-Trial/


![image](https://github.com/user-attachments/assets/95a83f32-47e7-4e4e-92a6-29b4dae29df9)


B4: Mở file vừa tải về, chạy file .exe để cài đặt.
B5: 


