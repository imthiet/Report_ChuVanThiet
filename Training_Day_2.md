
# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 2 Mục 13-15)

## Mục lục

- [1 DNS](#1-dns)
  - [1.1 Tìm hiểu về hệ thống DNS](#11-hệ-thống-dns)
  - [1.2 Các loại bản ghi sử dụng trong DNS](#12-các-loại-bản-ghi-trong-dns)

- [2 Triển khai ứng dụng Web](#3-triển-khai-ứng-dụng-web)
  - [2.1 Linux](#21-linux)
    - [2.1.1 Triển khai site wordpress trên LAMP stack](#211-triển-khai-site-wordpress-trên-lamp-stack)
     - [2.1.2 Triển khai site wordpress trên LAMP stack](#212-triển-khai-site-wordpress-trên-lemp-stack)
    
     - [2.1.3 Triển khai site wordpress tách web server, DB server](#214-triển-khai-site-wordpress-tách-web-server-db-server)
   
     - [2.1.4 Triển khai site wordpress trên LAMP stack](#213-triển-khai-site-wordpress-trên-lamp-stack)
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

### 2 Triển khai ứng dụng Web
#### 2.1 Linux
##### 2.1.1  Triển khai Site Wordpress trên Lamp stack
B1: Cài đặt Lamp stack:

  `sudo apt update`
  
  `sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php php-cli unzip curl -y`
  
 Đã cài thành công:
 
  ![image](https://github.com/user-attachments/assets/8b441917-e04d-4cc1-8d79-3dd17366c597)

 B2: Vào MySql
  
                                    `sudo mysql`

  -Tạo databse và user:
  `CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
  CREATE USER 'thietquang'@'localhost' IDENTIFIED BY '137203';
  GRANT ALL PRIVILEGES ON wordpress.* TO 'thietquang'@'localhost';
  FLUSH PRIVILEGES;
  EXIT; `
  - Kiểm tra user và database bằng cách đăng nhập vào `mysql -u thietquang -p` và `SHOW DATABASES`:

  ![image](https://github.com/user-attachments/assets/b1287418-5b5a-4efc-93f7-879a7e7b5f4c)
  
  B3: Cài đặt WordPress:
   - chuyển đến thư mục /tmp
   - truy vấn và tải theo url: https://wordpress.org/latest.tar.gz
   - giải nén file đã tải
   - copy thư mục wordpress vào /var/www/html
     
  `cd /tmp`
  
  `curl -O https://wordpress.org/latest.tar.gz`
  
  `tar -xvzf latest.tar.gz`
  
  `sudo cp -a wordpress/. /var/www/html/`

  Thư mục wordpress:
  
![image](https://github.com/user-attachments/assets/f69701b5-d676-4428-a44f-43fec303bb1e)

  B4: Cấu hình quyền truy cập:
    - Set quyền
    - Tìm kiếm các folder và đặt quyền 755 cho chúng trong /var/www/html
    - Tìm và phân quyền 644 tương tự nhưng là cho các file.
  
  `sudo chown -R www-data:www-data /var/www/html/`
  
  `sudo find /var/www/html/ -type d -exec chmod 755 {} \;`
  
  `sudo find /var/www/html/ -type f -exec chmod 644 {} \;`

  
  B5: Tạo file config cho Wordpress
  
  `cd /var/www/html`
  
  `cp wp-config-sample.php wp-config.php`

  - Chỉnh sửa file wp-config.php

  `sudo nano wp-config.php
`
  - Thay đổi Config trong file:
    
  `define('DB_NAME', 'wordpress');`
  
  `define('DB_USER', 'wpuser');`
  
  `define('DB_PASSWORD', 'strongpassword');`
  
  `define('DB_HOST', 'localhost');`

![image](https://github.com/user-attachments/assets/3fdd1494-18cc-4c1a-9bdf-bbf68c12657c)

  B6: Khởi động lại Apache

  `sudo systemctl restart apache2`
  
  -> Thành công triển khai 1 site WordPress với Lamp Stack

  ![image](https://github.com/user-attachments/assets/7a8ff2b7-a42d-4908-aa67-f133fdcdc74c)

  
![image](https://github.com/user-attachments/assets/bce67852-c7e2-4486-b597-37a7624db7ce)



##### 2.1.2  Triển khai Site Wordpress trên Lemp stack
- LEMP stack tương tự với LAMP nhưng thay vì APACHE là Nginx.
  B1: Cài đặt Nginx
   - Vì đã cài LAMP trước đó nên Mysql,PHP đã có sẵn nên chỉ cần cài thêm nginx,php-fpm(cho riêng nginx)

`
sudo apt install nginx php-fpm unzip curl -y`


  - Tắt APache để tránh xung đột với Ngnix( chung gate 80)

  `sudo systemctl stop apache2`

  `sudo systemctl disable apache2`

  - Khởi chạy ngnix thành công:
    
![image](https://github.com/user-attachments/assets/86d93b2c-cc8f-435f-a7ee-4507fe91681a)

  
  - Vì đã cấu hình Mysql, tải và giải nén Wordpress, chỉnh sửa wp-config.php trước đó nên giờ chỉ cần làm vài bước cáu hình cho LEMP.
  
  B2:Cấu Hình Nginx để trỏ đến thư mục Wordpress
  
  cấu hình cho file /etc/nginx/sites-available/wordpress:
 

`server { `

    listen 80;
    server_name localhost;

    root /var/www/html/wordpress;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
`}`

 B3: Kích hoạt site và reload nginx:
  
  `sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/`
  
    sudo nginx -t
    
    sudo systemctl reload nginx`
 

  B4: Truy cập localhost:192.168.80.128 để kiểm tra site:
  
  ![image](https://github.com/user-attachments/assets/ce4fe0d3-d329-43d5-bd32-103d9ea13674)


  -> Thành công triển khai site wordpress với LEMP stack!


#### 2.1.3 Triển khai site wordpress tách Web server, DB server
    
  
 

  
