
# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 4 Mục 18-19)

## Mục lục
### Monitor - Prometheus + Grafana

- [1 Cài đặt Prometheus, Grafana trên Ubuntu 22.04](#1-cài-đặt-prometheus-grafana-trên-ubuntu-2204)

  Prometheus: Công cụ mã nguồn mở để giám sát và cảnh báo, thu thập/lưu trữ dữ liệu time-series, 
  truy vấn bằng PromQL, tích hợp Alertmanager. Dùng giám sát server, ứng dụng, Kubernetes.
  
  Grafana: Nền tảng mã nguồn mở để trực quan hóa dữ liệu, tạo dashboard từ Prometheus và nhiều 
  nguồn khác. Hỗ trợ biểu đồ, cảnh báo, tùy chỉnh cao.

  Quan hệ: Prometheus thu thập dữ liệu, Grafana hiển thị dữ liệu qua dashboard.
  
  Ứng dụng: Giám sát và phân tích hệ thống, ứng dụng, container.

  - Cài đặt:
   B1: Tạo user và thư mục cho Prometheus

    ```
    sudo useradd --no-create-home --shell /bin/false prometheus
    sudo mkdir /etc/prometheus /var/lib/prometheus

    ```
  ![image](https://github.com/user-attachments/assets/ccdb7cad-0675-4a54-b046-a6f188268757)

   B2: Tải và cài đặt Prometheus
  
   ```
    cd /tmp
   
    wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-          2.52.0.linux-amd64.tar.gz
   
    tar -xvf prometheus-*.tar.gz
   
    cd prometheus-*.linux-amd64
   
    sudo cp prometheus promtool /usr/local/bin/
   
    sudo cp -r consoles console_libraries /etc/prometheus/
   
    sudo cp prometheus.yml /etc/prometheus/
    ```
   ![image](https://github.com/user-attachments/assets/fcaf9a9f-64c8-44a6-bb3f-19ef8a1f07ab)

    Đã tải thành công và chuyển đến thư /tmp/prometheus
    
    ![image](https://github.com/user-attachments/assets/e4aa60b9-17e0-45e9-82bf-2431df9dbfc7)

    B3: Tạo file service:

    `sudo nano /etc/systemd/system/prometheus.service`

    ![image](https://github.com/user-attachments/assets/57855b52-d8cc-4124-9183-35da0b5af55f)

    - thêm vào file service đó:
    
    ```

    [Unit]
    Description=Prometheus
    Wants=network-online.target
    After=network-online.target

    [Service]
    User=prometheus
    ExecStart=/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --              storage.tsdb.path=/var/lib/prometheus

    [Install]
    WantedBy=default.target

     ```

    ![image](https://github.com/user-attachments/assets/fe00c710-fde6-4a3c-a261-71fbe1cd47e5)


    B4: Khởi chạy Prometheus:
    
    ```
    sudo systemctl daemon-reexec
    sudo systemctl enable prometheus
    sudo systemctl start prometheus
    
    ```
    lệnh chạy:
    
    ![image](https://github.com/user-attachments/assets/1d7a74ea-1b3e-44b7-82eb-c1a92a3ef316)
    
    - Tiếp theo, 
    -Kết quả:

      ![image](https://github.com/user-attachments/assets/2f21043c-975e-4698-85b5-044c4cef0e53)
    
    B5: Cài đặt Grafana

    - Lệnh cài từ repo github chính thức của grafana:
      
    ```
    sudo apt install -y software-properties-common apt-transport-https
    
    sudo mkdir -p /etc/apt/keyrings
    
    wget -q -O - https://apt.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.key
    
    echo "deb [signed-by=/etc/apt/keyrings/grafana.key] https://apt.grafana.com stable main" |       sudo tee /etc/apt/sources.list.d/grafana.list
    ```
    ![image](https://github.com/user-attachments/assets/bfd41a64-2c07-43ff-a3d1-938155dc2da8)

    B6: cài đặt grafana:
    
    ```
    sudo apt update
    
    sudo apt install grafana -y
    ```

    - Đã cài thành công:
      
    ![image](https://github.com/user-attachments/assets/0296bbed-aa26-4275-8bb6-03556d285d22)

    - Khởi  động grafana:
    
    ```
    sudo systemctl daemon-reload
    
    sudo systemctl enable grafana-server
    
    sudo systemctl start grafana-server
    
    sudo systemctl status grafana-server

    ```
      
    - Kết quả: Running
    
    ![image](https://github.com/user-attachments/assets/5d73d49b-a6e6-497a-9656-49ef027d6750)
    
  B7: Truy cập thử prometheus và Grafana từ trình duyệt máy khác(Máy windown chủ luôn)
  
  Với ip: `192.168.80.134`

  ![image](https://github.com/user-attachments/assets/f592ee1d-e926-4676-8b44-7f18d1931004)

  - Với `http://192.168.80.134:9090`:

  ![image](https://github.com/user-attachments/assets/a0b5bc4e-9efe-4e6d-8881-5d34501a165a)

  - Với `http://192.168.80.134:3000`:
    
  ![image](https://github.com/user-attachments/assets/36c93d5a-91cf-4b57-9720-9c3bebfa4115)
  
  - Mặc định tài khoản-mật khẩu để login Grafana là `admin - admin`
  
  ![image](https://github.com/user-attachments/assets/c58c0cf4-ce3d-43f8-a043-a8c53d6a2b7c)

  -> tiếp theo có thể skip hoặc đổi mật khẩu -> tiếp tục vào trang chủ:

  ![image](https://github.com/user-attachments/assets/e0a4a99f-470a-4c00-96c6-82c737516f05)


-> Cài Đặt Thành công Prometheus và grafana.


- [2 Cấu trúc query Prometheus)](#2-cấu-trúc-query-prometheus)

  - Prometheus Query Language là ngôn ngữ dùng để truy vấn, giám sát dữ liệu tỏng Prometheus
  
  - Cấu trúc  metric đơn giản:
    
    `node_cpu_seconds_total`

  Lọc theo label: `node_cpu_seceonds_total{mode="idle"}`
  Tính toán giá trị: `rate(node_cpu_seconds_total{mode="idle"}[5m])`
  Và còn rất nhiều...
  
  -Thực hành 1 vài lệnh đơn giản:
  
  `up`  `node_load1` `rate(node_network_receive_bytes_total[5m])`
  
  `avg(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)`


  ![image](https://github.com/user-attachments/assets/8f33d06e-cd59-412d-8004-edd34169947f)

  
  ![image](https://github.com/user-attachments/assets/6cd0b1ca-a315-4f04-b91e-f3e629c27c84)

  ![image](https://github.com/user-attachments/assets/9f3879ca-582b-4dd8-b698-5c1a5fc1f9fa)

  - Kết quả hầu hết trả ra Empty result chứng tỏ chưa có node metric nào và chưa cấu hình node_exporter -> cần cài đặt thêm
  
  - Cài `node_exporter`:
  ```
  cd /tmp
  wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-        1.8.1.linux-amd64.tar.gz
  
  tar xvf node_exporter-1.8.1.linux-amd64.tar.gz
  
  cd node_exporter-1.8.1.linux-amd64
  
  sudo cp node_exporter /usr/local/bin/
  ```

  Cài đặt thành công!
  ![image](https://github.com/user-attachments/assets/c68a367a-033f-40f2-864a-a7ad14b77723)
  
  
  - tạo Service:
  Command: `sudo nano /etc/systemd/system/node_exporter.service`
  
  ![image](https://github.com/user-attachments/assets/24b21059-672f-45a5-826d-31f43d698b6b)

  Config cho file:
  ```
  [Unit]
  Description=Node Exporter
  After=network.target
  
  [Service]
  User=nobody
  ExecStart=/usr/local/bin/node_exporter
  
  [Install]
  WantedBy=default.target

  ```
  
  ![image](https://github.com/user-attachments/assets/0f59778a-87aa-458e-ad3f-f6849301fc41)

  
  
-> Khởi động: 
 ```
 sudo systemctl daemon-reload

sudo systemctl enable node_exporter

sudo systemctl start node_exporter

 ```

 Trạng thái running là đã thành công:

![image](https://github.com/user-attachments/assets/137648aa-e34c-4e21-a690-dc902ba94f3d)
 

 - Cấu hình Prometheus để thu thập từ `node_exporter`

  `sudo nano /etc/prometheus/prometheus.yml`
 - Chỉnh sửa thêm vào:
  ```
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
  ```

  ![image](https://github.com/user-attachments/assets/c3426ac1-0d7a-4537-af45-27ec3eba7d31)

  - Ctr + X để lưu và khởi động lại Prometheus: `sudo systemtcl restart prometheus`
    
  ![image](https://github.com/user-attachments/assets/49911ace-5ca7-42c7-a406-bb7157525e36)

  -  Kiểm tra lại và xem kết quả với promql: `node_cpu_seconds_total`
  -> Thành công !!
  
  ![image](https://github.com/user-attachments/assets/ed983d2d-84f2-437e-b514-04762076c1d7)

  ![image](https://github.com/user-attachments/assets/a1d7d8d8-b8c1-46fa-8448-884443f469ce)


- [3 Monitor server Linux](#1-monitor-server-linux)
  - Đã cài đặt `node_exporter` ở trên kia giờ chỉ cần kết nối Grafana với Prometheus để show       DashBoard
  B1: Vào grafana(`localhost:192.168.80.134:3000`) click chọn "Data Source"
  
  ![image](https://github.com/user-attachments/assets/34260ad2-8ee4-4f8d-b990-d383deee8aaf)

  - Chọn prometheus, nếu không thấy gợi ý thì có thể nhấn tìm kiếm:

  ![image](https://github.com/user-attachments/assets/f6194451-78f4-48c3-b2a1-b833fc49c84a)

  - Sau đó sẽ đucợ chuyển tới tab setting, tại đay nhập địa chỉ của prometheus:

  ![image](https://github.com/user-attachments/assets/00ee4bd9-ce85-485d-acfb-816824949bb1)

  
  - Kéo xuống chọn `Save & test`:

  ![image](https://github.com/user-attachments/assets/abf34cc4-ff90-432d-b2d2-b31dbe98dbe5)


  - Thông báo thành công!

  ![image](https://github.com/user-attachments/assets/c0d24096-6c4b-40c4-975f-5795375a0d1c)

  - Ta import dashboard để hiển thị:
  Nhấn dấu + và chọn Import:

![image](https://github.com/user-attachments/assets/1e1dcc12-1b38-4293-9c9e-a0801582cca1)


  - Nhập ID dashboard là 1860( Node Exporter Full) -> Nhấn Load

  ![image](https://github.com/user-attachments/assets/8280f55f-8aeb-4a4b-8677-69974565692b)

  - Khi nhấn laod sẽ được chuyển tới trang tiếp theo, tại đây cần import Prometheus:
  
  ![image](https://github.com/user-attachments/assets/1b135f27-5c3a-452f-b16a-ea4328edae33)



  -Import -> 

  
  ![image](https://github.com/user-attachments/assets/d06e0286-f13a-4d24-bb58-89a060864d36)


  - Ta đã có 1 trang Monitor để theo dõi server:

  ![image](https://github.com/user-attachments/assets/351186e5-5950-4e8c-887f-e16c2f7e78d8)

  

- [4 Monitor mysql](#4-monitor-mysql)

  Việc monitor Mysql sẽ theo dõi được hiệu xuất, kết nối, query,cache,innodb,table size,... Dữ liệu được hiển thị trên Grafana.

   B1: Cài đặt `mysql_exporter` trên server có MYysql
  
     Vì chưa có Mysql nên ta cần cài đặt.
      
      ![image](https://github.com/user-attachments/assets/97158537-4553-4730-9d81-6e2edc23e115)
    
    Tiếp theo dùng lệnh `sudo apt install mysql-server` > nhấn Y để xác nhận cài đặt.
    Cài đặt thành công:

    ![image](https://github.com/user-attachments/assets/f77204fd-38ea-47f2-9c93-4a776bbad1af)

    - Tiếp theo ta cài đặt `mysql_exporter`

    Command: 
    ```
   cd /tmp
  wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.15.1/mysqld_exporter-0.15.1.linux-amd64.tar.gz
  tar -xzf mysqld_exporter-0.15.1.linux-amd64.tar.gz
  sudo cp mysqld_exporter-0.15.1.linux-amd64/mysqld_exporter /usr/local/bin/


    ```
    
  ![image](https://github.com/user-attachments/assets/bd153161-b708-4fe8-9d23-596160bf844c)

- cài đặt thành công:
  
  ![image](https://github.com/user-attachments/assets/623cc6da-4138-4351-b03c-e0fe2bd5a06d)


  B2: Tạo User Mysql cho exporter
  Tên: exporter password: Thietquang123!
  
  ![image](https://github.com/user-attachments/assets/abf675d1-7b47-4748-884e-2dcb3379fbde)

  B3: Tạo file `.my.cnf` để chứa credentials

  `sudo nano /etc/.mysqld_exporter.cnf
`
  - Nội dụng file: 
  ```
  [client]
  user=exporter
  password=your_password
  
  ```  
  ![image](https://github.com/user-attachments/assets/f647a65e-c83a-4657-beff-e28418d57b3d)

  - Phân Quyền: 

  ```
  sudo chown root:root /etc/.mysqld_exporter.cnf
  sudo chmod 600 /etc/.mysqld_exporter.cnf
  
  ```  

  ![image](https://github.com/user-attachments/assets/54680330-b87d-4f1b-9844-7ec3131b2b33)

  Bước 4: Tạo systemmd service cho mysql_exporter

  `sudo nano /etc/systemd/system/mysqld_exporter.service`

  Nhập cấu hình:
  
  ```
  [Unit]
  Description=Prometheus MySQL Exporter
  After=network.target
  
  [Service]
  User=nobody
  ExecStart=/usr/local/bin/mysqld_exporter \
    --config.my-cnf=/etc/.mysqld_exporter.cnf
  
  [Install]
  WantedBy=multi-user.target

  ```

  
    
  ![image](https://github.com/user-attachments/assets/a9140d23-0094-466e-9b87-fcdd06d62646)

  Bước 5: Khởi động lại Exporter:
  
  ```
  sudo systemctl daemon-reload
  sudo systemctl enable mysqld_exporter
  sudo systemctl start mysqld_exporter

  ```

    ![image](https://github.com/user-attachments/assets/7e3ed39b-210f-411c-8c1c-b51e2416cc53)

  Bước 6: Thêm vào Prometheus:

  - Sửa file prometheus.yml `sudo nano /etc/prometheus/prometheus.yml`

  - thêm dưới scrape_configs: 
  ```
    - job_name: "mysql"
    static_configs:
      - targets: ["localhost:9104"]


  ```  

  ![image](https://github.com/user-attachments/assets/908306db-8d37-46b7-89e7-2fe0aed7fd65)

  - Khởi động lại Prometheus:
    
  `sudo systemctl restart prometheus`
  
    ![image](https://github.com/user-attachments/assets/681d8244-73fe-4bbf-ae87-9726df3077f9)

  Bước 7: Import DashBoard Grafana cho Mysql
  
  Tương tự như phần import prometheus
  - Grafana -> dấu + -> Import

  ![image](https://github.com/user-attachments/assets/e26da9cb-ba1c-4b93-929c-1a9957f8ecf3)

  - Nhập ID: 7362(Mysql Overview by Percona)
    
    ![image](https://github.com/user-attachments/assets/033ce4e0-2c69-44ee-97e2-9067dfdd1fb8)

  - Chọn prometheus là data source:

    ![image](https://github.com/user-attachments/assets/5cbdf8bd-ae01-4378-9ea1-f163a41bfb94)

  - Thành công dù chưa có dữ liệu nào của Mysql để hiển thị:
  
  ![image](https://github.com/user-attachments/assets/438e2960-650f-4f37-afc3-f02380124f39)



  


- [5 Đưa biểu đồ lên kết hợp với grafana](#5-đưa-biểu-đồ-lên-kết-hợp-với-grafana)

  B1: Truy cập vào grafana (`locahost:192.168.80.134:3000)
  -> Create New DashBoard:
  
  ![image](https://github.com/user-attachments/assets/b5ada2ee-8bca-412a-9282-5b35695625f3)

  B2: Chọn Add visualization

  ![image](https://github.com/user-attachments/assets/6d2db428-dc31-4c21-93e9-93ca59b738af)


  B3: Chọn soure: Prometheus
  B4: Thêm Query vào Promt này và nhấn run query:
  
  ![image](https://github.com/user-attachments/assets/2814f460-a32d-4932-9bea-bdefb70fdc17)
  
  
  B5: Thêm các câu lệnh để hiển thị CPU %, Mysql,... cùng 1 lúc:
  
  ![image](https://github.com/user-attachments/assets/c6242886-a41c-49d1-a13f-70e237110a3e)


  -> Kết Quả:

  ![image](https://github.com/user-attachments/assets/3e734d46-0a24-4d0c-bb51-14e39acd6691)

 

  
