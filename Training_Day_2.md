
# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 2 Mục 13-15)

## Mục lục

- [1 DNS](#1-dns)
  - [1.1 Tìm hiểu về hệ thống DNS](#11-hệ-thống-dns)
  - [1.2 Các loại bản ghi sử dụng trong DNS](#12-các-loại-bản-ghi-trong-dns)

- [2 Triển khai ứng dụng Web](#2-triển-khai-ứng-dụng-web)
  - [2.1 Linux](#21-linux)
    - [2.1.1 Triển khai site wordpress trên LAMP stack](#211-triển-khai-site-wordpress-trên-lamp-stack)
     - [2.1.2 Triển khai site wordpress trên LEMP stack](#212-triển-khai-site-wordpress-trên-lemp-stack)
    
     - [2.1.3 Triển khai site wordpress tách web server, DB server](#213-triển-khai-site-wordpress-tách-web-server-db-server)
   
     - [2.1.4 Bash Script cài đặt LAMP,LEMP stack](#214-bash-script-cài-đặt-lamp-lemp-stack)
  - [2.2 Windows](#22-windows)
    - [2.2.1 Triển khai site demo1 html basic trên web server IIS](#221-triển-khai-site-demo1-html-basic-trên-web-server-iis)
    - [2.2.2 Triển khai site demo2 asp classic trên web server IIS](#222-triển-khai-site-demo2-asp-classic-trên-web-server-iis)
    - [2.2.3 Triển khai site demo3 .net(3.5, 4.x) trên web server IIS](#223-triển-khai-site-demo3-net35-4x-trên-web-server-iis)
    - [2.2.4 Triển khai site demo4 php trên web server IIS](#224-triển-khai-site-demo4-php-trên-web-server-iis)
             
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
##### 2.1.1 Triển khai Site Wordpress trên Lamp stack
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



##### 2.1.2 Triển khai Site Wordpress trên Lemp stack
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
    
- Hướng làm: Sử dụng Docker để tạo 2 container riêng biệt:
  - 1 container chạy Mysql( db server)
  - 1 container chạy WordPress( web server)
B1: Cài Docker
    Chạy lệnh với quyền root

  `apt update `

  ` apt install docker.io docker-compose -y`

  ` systemctl enable docker`
  
  ` systemctl start docker`
  
Cài đặt thành công:

![image](https://github.com/user-attachments/assets/9a3febd2-637d-4d20-aa96-aff6a61500de)

 B2: Tạo file yml cấu hình docker-compose.yml   

  `mkdir wordpress-separated`
    
  `cd wordpress-separated`
  
  `nano docker-compose.yml`
  

![image](https://github.com/user-attachments/assets/6e7441b4-dcca-4e91-9fbf-9a0332763a89)

  - Cấu hình file yml vừa tạo:


  
```
version: '3.8'
services:
  db:
    image: mysql:5.7
    container_name: DB_server
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: thietquang
      MYSQL_PASSWORD: 137203
      MYSQL_ROOT_PASSWORD: 137203
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - wp_network

  wordpress:
    image: wordpress:latest
    container_name: Web_server
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: thietquang
      WORDPRESS_DB_PASSWORD: 137203
    depends_on:
      - db
    networks:
      - wp_network

volumes:
  db_data:

networks:
  wp_network:
```

*Note: dùng bản mysql 5.7 do tính ổn định thường dùng cho wordpress( hoàn toàn có thể dùng bản mới 8.0)
 - Có thể thấy phiên bản tệp docker: 3.8
 - Phần service với 2 container cần thiết đã được tạo: db và web. Với đầy đủ thông tin name,image, cài đặt restart nếu lỗi và phần Environment - thông tin theo đúng của mình.

 - Các phần thiết lập Volumn, Netswork: Container db và wordpress cso thể giao tiếp với nhau thông qua mạng này. Tên conatiner( Db_server, Web_server) có thể dùng như tên DNS nội bộ.  

![image](https://github.com/user-attachments/assets/55e577c3-bdfa-452b-aa84-8789fce17e69)

 
B3: Chạy ứng dụng với lệnh:

`docker-compose up -d`

Đợi pull và extract các libs

![image](https://github.com/user-attachments/assets/3a9315ba-fbd1-4fd1-92b4-f78c5e6b93af)

Kiểm tra trạng thái với `docker ps` để đảm bảo đã run( wordpress cổng 8080 còn mysql là 3306:

![image](https://github.com/user-attachments/assets/f5ad5f33-b488-4a3b-a89e-575c87f94f0b)

Truy cập vào http://localhost:8080 và kiểm tra trạng thái web:

![image](https://github.com/user-attachments/assets/66c866ea-fd3f-4d8d-8115-00ff2990d77f)


Đã thành công khởi chạy site wordpress tách riêng DB và Web.

Lệnh Pause Conatiner trên docker: ` docker pause + container name`
Sau đó kiểm tra trang thái( status )

![image](https://github.com/user-attachments/assets/0b5293a6-fba1-4f62-a587-2dcd11625004)


##### 2.1.4 Bash script cài đặt LAMP,LEMP stack

- Tạo file lamp.sh và lemp.sh, ở đây tôi tạo ở thư mục Desktop:
  ![image](https://github.com/user-attachments/assets/21688b47-87b5-45ad-9d26-712e4a36d33b)
  
- Chỉnh sửa 2 file đó, dùng `nano lamp.sh` và `nano lemp.sh`( lưu ý hiện tại đang ở thư mục chứa 2 file đó là Desktop)

![image](https://github.com/user-attachments/assets/ca0aed66-37dd-4b56-994a-7458db5754b7)

- Bash Script có syntax khá giống với ngôn ngữ PHP.
  - lamp.sh:
![image](https://github.com/user-attachments/assets/7a7a9fac-359f-44ed-b7e6-70569a3cfd6d)

Script code:

```
!/bin/bash

echo "========== Install LAMP Stack (Apache, Mysql, PHP) =========="

# update system

sudo apt update -y && sudo apt upgrade -y

# Install Apache

sudo apt install apache2 -y

# Install Mysql(thiet lap an toan)
sudo apt install mysql-server -y
sudo mysql_secure_installation 

# Install PHP and other module
sudo apt install php libapache2-mode-php php-mysql -y

# start and enable service

sudo systemctl enable apache2
sudo systemctl enable mysql
sudo systemctl restart apache2

# Notification

echo "===== LAMP installed ======"
echo "===== Try: http://localhost"

# create PHP test file 
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php > /dev/null
```
  -lemp.sh:
  
![image](https://github.com/user-attachments/assets/44fabccc-f00b-464d-822f-0a32beb54006)

Code Script:

```
#!/bin/bash

echo "=== Isntall LEMP stack (Nginx, MySQL, PHP) ==="

# Update
sudo apt update -y && sudo apt upgrade -y

# Install  Nginx
sudo apt install nginx -y

# Install  MySQL
sudo apt install mysql-server -y
sudo mysql_secure_installation

# Install PHP and other modules
sudo apt install php-fpm php-mysql -y

# Config Nginx for PHP
cat <<EOF | sudo tee /etc/nginx/sites-available/default
server {
    listen 80 default_server;
    root /var/www/html;
    index index.php index.html index.htm;

    server_name _;

    location / {
        try_files \$uri \$uri/ =404;
    }

    location ~ \.php\$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php$(php -v | grep -oP '\d+\.\d+' | head -1)-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
EOF

# Restart service
sudo systemctl restart nginx
sudo systemctl enable nginx
sudo systemctl enable mysql

echo "=== LEMP installed ==="

echo "Try: http://localhost"

# Craete file test PHP
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php > /dev/null

```

## 2.2 Windows
 - Với hệ điều hành windows, có vẻ như việc triển khai web dễ dàng hơn với GUI.
### 2.2.1 Triển khai site demo1 html basic trên web server IIS

B1: Bật IIS nếu chưa bật
 - Control Panel -> Program-> Turn Windows feature on or off
 - Chọn Internet Information Services, Web management Tools, World Wide Web Services
    
![image](https://github.com/user-attachments/assets/fae91301-225d-4b08-9d43-27b1bd92039d)

  - Nhấn OK -> Restart để apply changes:
    
  ![image](https://github.com/user-attachments/assets/1dc18c47-f0e4-45b3-868e-9f389c49a1eb)

B2: Tạo file HTML demo1
  - Tạo folder chứa file demo1: C:\interpub\wwwroot\demo1

  ![image](https://github.com/user-attachments/assets/a398c643-c3ae-4cdf-8917-d68f9f93288c)


  - Thêm file index.html để làm trang giao diện.
  - Code demo:
    ```   
    <!DOCTYPE html>
    <html>
    <head>
        <title>Demo1 Site</title>
    </head>
    <body>
        <h1>Demo1 Site - IIS Windows!</h1>
    </body>
    </html>
    
    ```
  ![image](https://github.com/user-attachments/assets/d5ce93b7-db8c-4207-8ce2-8626ee522f02)

  B3: Thêm website vào IIS
  

  
  - Mở IIS Manager:

    - Nhấn Win + R, gõ inetmgr, Enter.

![image](https://github.com/user-attachments/assets/992ad1fc-6166-40d6-8ca6-f512fc1f70e7)

  - Trong panel bên trái:

    - Click chuột phải vào Sites → chọn Add Website...
    - 
    ![image](https://github.com/user-attachments/assets/01381748-c349-4e3c-9b42-c52d8c7d69c6)


  - Nhập thông tin:
    
    ![image](https://github.com/user-attachments/assets/ab3e4afc-4374-4c81-b536-66de3094e6d6)

    - Site name: demo1
    
    - Physical path: C:\inetpub\wwwroot\demo1
    
    - Port: để mặc định là 80 (hoặc đổi nếu cần)
    
    - Host name: (tuỳ chọn) demo1_html
    - 
    ![image](https://github.com/user-attachments/assets/30fcada9-cf03-4bcc-acf3-909cd905c58a)

  - Nhấn OK để tạo website.
    
  B4: Truy cập vào web để kiểm tra:
  
http://localhost/demo1/

  ![image](https://github.com/user-attachments/assets/713ac53a-f203-4683-8960-a7e063aa3031)

  

### 2.2.2 Triển khai site demo2 asp classic trên web server IIS

- Tương Tự với phần trên, bật IIS - World Wide Web Services - ASP
  
   ![image](https://github.com/user-attachments/assets/61ae3783-0c89-433c-b46a-2068e516f969)
- tạo file demo2 .asp để demo nằm trong thư mục: C:\inetpub\wwwroot\demo2
- Tạo file index.asp tương tự
  
![image](https://github.com/user-attachments/assets/4e11f947-ba41-4ba0-ac0e-97189f5b1635)

![image](https://github.com/user-attachments/assets/6e50365f-c389-4883-ada7-5e478ee427e3)

- Thêm site vừa tạo vào IIS
  - Mở IIS Manager: Windows + R -> inetmgr -> Enter
  - LÀm tương tự như với site html như trên.( Lưu ý port 80 vì site html đang dùng)
    
  ![image](https://github.com/user-attachments/assets/bc563e0f-ffe4-4083-ab7a-6752afa3fd5a)

  Truy cập: Localhost/demo2/

  Kết quả:
  
![image](https://github.com/user-attachments/assets/c1ce1157-b494-4a96-9058-8ed5fec5dec5)

  

### 2.2.3 Triển khai site demo3 .net(3.5, 4.x) trên web server IIS

- Tương Tự với phần trên, bật IIS - World Wide Web Services - ASP.NET(3.4 và 4.8)
- Tạo file index.aspx trong folder C:\inetpub\wwwroot\demo3
- 
  ![image](https://github.com/user-attachments/assets/b054c60f-7d6b-4b02-b8f0-fffcac854298)
  
- Thêm Site vào IIS manager tương tự như trên. Chú ý path và port để tránh xung đột hoặc lỗi 404 
- Kết quả:

![image](https://github.com/user-attachments/assets/f334ed72-c61b-4f99-83ba-173bca1a2027)



### 2.2.4 Triển khai site demo4 php trên web server IIS


- PHP không nằm sẵn trong Windows feature để bật nền cần cài PHP
  - Tải PHP từ:  https://windows.php.net/download/ (bản thread safe)

![image](https://github.com/user-attachments/assets/782b57ca-90af-43cc-b530-b1dcacfe1f8a)




  - Giải nén vào thư mục C:\php(tùy chỉnh)
  
![image](https://github.com/user-attachments/assets/7fd7138f-0bd7-4bd8-8a27-051d535e6f43)


  - Thêm PHP vào system Path:
    - Window + R -> sysdm.cpl -> Enter
    - Chuyển sang tab Advanced -> Environment variables

    ![image](https://github.com/user-attachments/assets/f96e0ce7-7f3b-4af1-95ec-de61103f25dd)
    

    - Ở System variables chọn Path -> edit

    ![image](https://github.com/user-attachments/assets/2785b081-101b-49f5-8b14-a7814fc55fc4)
    
    - Thêm Path C:\php vào và Nhấn OK -> OK!
    - Kiểm tra bằng `php -v`:

    ![image](https://github.com/user-attachments/assets/36b50c30-387b-411b-8eea-5aaad1aeb0e2)

    
  - Cấu hình IIS để xử lý .php bằng FastCGI
    - Mở cmd -> inetmgr ->Enter
    - Chọn server ở side trái
    - mở handle Mapping
    
    ![image](https://github.com/user-attachments/assets/f9a220cd-2e78-421a-8d2e-1905d1eb6dc2)

  
    - Ở khung bên phải -> chọn Add Module Mapping

    ![image](https://github.com/user-attachments/assets/6a3efb3a-5a49-4c35-a5ef-cb33b5666bf9)

      - Request path: *.php
      - Module: FastCgiModule
      - Executable: C:\php\php-8.4.7-Win32-vs17-x64\php-cgi.exe
      - Name: PHP_via_FASTCGI
    - CLick OK - > YES
  - Kiểm tra FastCGI: mở FastCGI SETTING -> kiểm tra có đường dẫn C:\php\php-cgi.exe. Nếu chưa có thì ADD Aplication
  
    ![image](https://github.com/user-attachments/assets/a1c8c2c2-cf04-4a00-8d37-d733e048429b)

  
  -Restart IIS: mở cmd quyền admin và chạy   `iisreset `
  
  ![image](https://github.com/user-attachments/assets/bbaa5c14-532a-4473-abc8-dab8f3b0afcd)

  
- Kiểm tra hoạt động với file phpinfo :
![image](https://github.com/user-attachments/assets/12224693-80c9-498c-96d3-f949de45839f)

chạy http://localhost/demo4/info.php để kiểm tra ->  thành công

  
  ![image](https://github.com/user-attachments/assets/a90832cf-99d8-4ccf-a0d6-e101b6f013e7)



