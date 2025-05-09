
# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 5 Mục 19-20)

## Mục lục
### NFS Server
  

- [1 Tìm hiểu về NFS Server](#1-tìm-hiểu-về-nfs-server)

- [2 Kết nối NFS CLient với NFS server](#2-kết-nối-nfs-client-với-nfs-server)

### 1 Tìm hiểu về NFS server

- Tổng quan về NFS - Net Work File System.
  -  NFS là địch vụ để chia sẻ thư mục giữa các máy Linux trong mạng nội bộ. Đây là giải pháp phổ biến để chia sẻ File giữa các server và client trong hệ thống
  -  Cụ thể hơn: NFS server chứa thư mục được chia sẻ, cho phép máy khác truy cập.
                 NFS client gắn mount thư mục được chiaw sẻ từ server vào hệ thống của mình như                     một thư mục nội bộ 
A. Cài đặt và cấu hình NFS Server( trên máy Ubuntu linux server 20.04)

B1: Cài đặt gói NFS Server

`sudo apt update `

`sudo apt install nfs-kernel-server -y`

![image](https://github.com/user-attachments/assets/42649de5-536a-4284-9531-b9b0f9387b6e)

  

B2: Tạo thư mục chia sẻ

Command:

```
sudo mkdir -p /srv/nfs/shared

sudo chown nobody:nogroup /srv/nfs/shared

sudo chmod 777 /srv/nfs/shared
```

![image](https://github.com/user-attachments/assets/8da97489-7b17-41d0-b540-ae97ffacb3d0)



B3: Cấu hình thư mục chia sẻ

  - Mở file: `sudo nano /etc/exports`
    
  - Thêm dòng vào file exports:    `/srv/nfs/shared 192.168.80.0/24(rw,sync,no_subtree_check)`
  - 192.168.80.0/24 là dải IP mạng LAN của mình( có thể thay bằng IP client nếu chỉ cho 1 máy)
  
  ![image](https://github.com/user-attachments/assets/68f843f6-976f-4ddf-9d7c-a277d0d6a304)

  
  - Ctr + o để lưu

B4: Khởi động lại NFS Server

```
sudo exportfs -a

sudo systemctl restart nfs-kernel-server
```

![image](https://github.com/user-attachments/assets/db5f235e-98b8-4af2-b9da-d563b92098d2)


B5: Kiểm tra xuất khẩu thư mục:

command: `sudo exportfs -v`

![image](https://github.com/user-attachments/assets/4b36693d-d2ba-4834-b325-fd82370c887f)


-> Đã Hoàn tất việc cài đặt NFS Server


### 2 Kết nối NFS Client với NFS server

- Đã có 1 máy ubuntu với ip: 192.168.80.130/24 (cùng dải mạng)
  
![image](https://github.com/user-attachments/assets/88f094d1-72ea-44ca-bf95-ac8908b762f4)

  
B1: Cài đặt gói NFS client trên máy client

Command:

```
sudo apt update
sudo apt install nfs-common -y

```

B2: Tạo thư mục mount

- Ta sẽ mount vào /mnt/nfs_shared
  
`sudo mkdir -p /mnt/nfs_shared
`

![image](https://github.com/user-attachments/assets/1dd2450b-a2f0-4d17-9139-a15ba1a0be3d)


B3: Mount thư mục từ NFS server

`sudo mount 192.168.80.134:/srv/nfs/shared /mnt/nfs_shared`

Note: 192.168.80.134 là IP cảu NFS server
      /srv/nfs/shared là thư mục chia sẻ bên server
      /mnt/nfs_shared là nơi thư mục xuất hiện bên client

![image](https://github.com/user-attachments/assets/d84e93d5-61ee-4bb1-8ae1-071ee242873e)


B4: Kiểm tra lại:
  
`df -h | grep nfs
`
![image](https://github.com/user-attachments/assets/29d72e51-5fd0-4fca-aaec-718165f2de94)


- Thử tạo một file:

```
cd /mnt/nfs_shared
touch test.txt
```

![image](https://github.com/user-attachments/assets/46dfb315-df28-44b4-a04b-b1126e83b2eb)

-> Tạo được file với  `touch` vì phia trên đã phần quyền cho thư mục shared là 777 ( quyền r-w-x cho tất cả nhóm)

-> Thành công với NFS server!



























