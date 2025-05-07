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
    
  - [2.2 Truy Cập Admin và EndUser](#22-truy-cập-admin-và-enduser)
    


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



========= Gặp Lỗi và Đang trong quá trình Fix!!! =========

- Note lại thông tin: thietquang.com - 192.168.80.30 mail.thietquang.com mail
- Bắt đầu tạo lại với 1 máy server mới hoàn toàn.
- Sau các bước cài đặt package, config resolv, tạo DNS local -> chỉnh sửa file /etc/bind/named.conf.local
  
![image](https://github.com/user-attachments/assets/386fad5c-b2a4-4d22-92ba-fd241bada9aa)


- Tạo và chỉnh sửa file /var/lib/bind/thietquang.com.hosts

` vi /var/lib/bind/thietquang.com.hosts`


![image](https://github.com/user-attachments/assets/938358d8-a511-4980-b36c-bc04762c13c2)

- Gặp lỗi khi kiểm tra: `sudo named-checkzone thietquang.com /var/lib/bind/thietquang.com.hosts


 
 ` zone thietquang.com/IN: has no NS records  `

` zone thietquang.com/IN: not loaded due to errors`


- Kiểm tra trạng thái của named.service thì thấy Active đã failed

 ![image](https://github.com/user-attachments/assets/1a830bbc-a9b1-4228-9dd8-1828cee25924)

- Sau khi kiểm tra một loạt thì ra lỗi nằm syntax của file named.conf.local: allow tranfor trong khi cú pháp đúng là `allow transfer`

  ![image](https://github.com/user-attachments/assets/99ab9ac0-30ea-4aa9-ab05-edf745e562e9)

- Sau khi sửa và kiểm tra thì trạng Thái Active đã Runing:

![image](https://github.com/user-attachments/assets/9eeeb9e3-7d82-4dad-8861-d48e763b7b87)

- Sau đó enable và `nslookup mail.thietquang.com` để kiểm tra DNS :

![image](https://github.com/user-attachments/assets/ff11e6cc-27b5-42a3-83e8-6421cab47014)

-> Thành công 1/3 quá trình

Bước tiếp theo: cài đặt Zimbra:
  - Tải Zimbra:
`wget https://files.zimbra.com/downloads/10.1.0_GA/zcs-NETWORK-10.1.0_GA_4655.UBUNTU22_64.20240819064312.tgz`

Dính lỗi not found:

![image](https://github.com/user-attachments/assets/4f5fb651-e10c-4292-885e-b744842600aa)

-Fix thành công:

![image](https://github.com/user-attachments/assets/7fbd6f8c-a4ec-41bd-a78f-41e4eec73381)

- sau khi download, ta giải nén:

`tar -zxvf zcs-10.1.4_GA_4200000.UBUNTU22_64.20241224171952.tgz`

`cd zcs-10.1.4_GA_4200000.UBUNTU22_64.20241224171952`

- sau đó Nhấn Y để đồng ý điều khoản -> Y -> Y tiếp để đồng ý:

![image](https://github.com/user-attachments/assets/7f5d8d7c-af72-4dd1-84e5-0dd4b99ebe20)

![image](https://github.com/user-attachments/assets/02ac9f84-d744-499d-a6dc-cd39eb72d011)

- Lựa chọn cài đặt các Package:

![image](https://github.com/user-attachments/assets/0f552a75-4dbd-4917-8ef8-0499cfc8a3be)


- Y tiếp để chấp nhận Modified:

![image](https://github.com/user-attachments/assets/f54bd1c0-3841-4206-941c-7fb1b85269cf)

- Sau đó nhập y để thay đổi domain ( bỏ mail. ở phia trước)
Tuy nhiên bị báo lỗi:

```
t is suggested that the domain hame have an Mx record configured in DNS
e-Enter domain name? [Yes] y
Create domain: [mail. thietquang.com] .thietquang.com
empty domain label at /opt/zimbra/common/lib/per15/Net/DNS/Question.pm line 80.
oot@mail:/opt/zcs-NETWORK-10.1.0_GA_4655.UBUNTU22_64.20240819064312#

```
sau đó thử chạy lại file ./install.sh thì gặp lỗi:

![image](https://github.com/user-attachments/assets/0ea1636e-0563-40bd-983e-98a242f50e09)


- Chưa thể fix được lỗi này!


### 1.2 Khởi tạo User Zimbra
### 1.3 Thiết lập chính sách về mật khẩu account email
### 1.4 Thiết lập chữ ký Zimbra email
### 1.5 Thiết lập forward email Zimbra

## 2 Triển khai ứng dụng mail server MDaemon
### 2.1 Cài đặt Email Server Mdaemon trên Windows server

B1: Tải file Iso của windows Server từ trang web chính thức của Microsoft:
https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022

B2: Thiết lập nó lên VMWare làm máy ảo( lưu ý cấu hình cho máy ảo ram ít nhất 2GB, Bộ nhớ 40GB)

![image](https://github.com/user-attachments/assets/9f12b282-d923-4998-b8a7-5e672aef869a)

![image](https://github.com/user-attachments/assets/bef0f4ee-06c4-4287-b34e-82d8a68c760f)

![image](https://github.com/user-attachments/assets/2f6e81f8-3319-4d50-8253-35959d55d65a)

- Giao diện chính:

![image](https://github.com/user-attachments/assets/98da8745-4642-48a1-b358-82afe206afc9)

-> Đã cài đặt thành công!


B3: Tải và cài đặt MDaemon Mail Server bản Trial  từ trang chính thức:

![image](https://github.com/user-attachments/assets/841a4e57-09cd-46e7-bcce-f1c8f8ccd247)

- Mở file và cài đặt:

![image](https://github.com/user-attachments/assets/ccb41a0a-f4e7-4a41-9c8f-3686dc46f82d)


- Lựa chọn Next->next->Trial version->next và đến phần Customer Infomation :

![image](https://github.com/user-attachments/assets/e7924e3f-cd27-4132-a027-8b9c5a63e182)


- Ở đây cần nhập đúng thông tin email để nhận mã Trial key qua mail:

![image](https://github.com/user-attachments/assets/3898875c-ed2d-4f2f-8f9f-10712522324c)


- Lấy mã được gửi qua mail:


![image](https://github.com/user-attachments/assets/15696151-b9ae-4b0c-944f-270d292d3658)


- Nhập mã vào ô yêu cầu -> Next:
  
![image](https://github.com/user-attachments/assets/6e8aa92a-4fd0-4bae-a1ac-12378bc02c5d)

- Thông báo thành công và sẵn sàng Install:

![image](https://github.com/user-attachments/assets/b1e9ec52-48ff-4045-b9f7-f0651a0762d2)



- Đợi quán trình cài đặt hoàn tất:

![image](https://github.com/user-attachments/assets/db9ad3c9-465e-40a9-a83e-2e9f41b92bde)



- Cài đặt Domain name và host name. ở đâu làm LAB để gửi thử trong nội bộ nên có thể dùng tên ảo. Nếu cần gửi qua iternet thì cần cấu hình phù hợp với DNS.

![image](https://github.com/user-attachments/assets/9c39913a-9f0c-46f2-b1ea-f2e086cdee44)

- Tiếp đến là thiết lập Tài khoản Admin để toàn quyền quản trị

![image](https://github.com/user-attachments/assets/442e623c-b00d-4346-b22e-63b400c76ad1)


- Tiếp theo bấm Next.
- hoàn thành cài đặt -> Finish:

![image](https://github.com/user-attachments/assets/7a3c6091-3fbd-40a3-8bbb-60d1948a38ea)





### 2.2 Truy cập Admin và Enduser

- Sau khi cài đặt thành công sẽ được chuyển tới trang Login để truy cập.
  
`localhost:1000/login.wdm`

![image](https://github.com/user-attachments/assets/bbd90f5c-2272-478e-b414-ab66aa541f90)

- Sau khi đăng nhập thành công:

![image](https://github.com/user-attachments/assets/5215e6db-a644-4bae-b28b-e0a6c0c08f43)


- Tạo tài khoản người dùng - EndUser

 - Vào tab Account Manager:
   
  ![image](https://github.com/user-attachments/assets/2c97135f-aaa5-44ac-83ad-60732ee2292f)

 - Chọn New :
   
  ![image](https://github.com/user-attachments/assets/dee07148-71ff-4327-80bb-f516bde1f39e)

 - Nhập thông tin cho end user account:

![image](https://github.com/user-attachments/assets/064c9752-9748-46ee-8036-455760c0f9ee)

 - Chọn Save And Close trên thanh công cụ -> thành công:

![image](https://github.com/user-attachments/assets/05f43e17-76ad-4a14-bda5-e3cc7ea41dc8)

 - Có thể thấy user vừa được tạo trong account list:

  ![image](https://github.com/user-attachments/assets/971675c6-2c0e-4ac7-809e-70c18a40e3ab)

 - Truy cập vào `localhost:3000` để đăng nhập với Account End USer

  ![image](https://github.com/user-attachments/assets/0dd46954-a69f-4845-a343-49cbd603154c)

 -  Nhập tài khoản -> Sign In

![image](https://github.com/user-attachments/assets/d2856138-2e4d-477a-823a-8f17a6d6d1de)

 - Đăng nhập thành công vào trang cho user:

![image](https://github.com/user-attachments/assets/207f5a34-84c7-41d9-9c0b-8e1fae83e891)





 

 
 




