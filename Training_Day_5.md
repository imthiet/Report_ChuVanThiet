
# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 5 Mục 19-20)

## Mục lục
### NFS Server
  

- [1 Tìm hiểu về NFS Server](#1-tìm-hiểu-về-nfs-server)

- [2 Kết nối NFS CLient với NFS server](#2-kết-nối-nfs-client-với-nfs-server)

- [3 LVM](#3-lvm)
  - [3.1 Tạo physical volume](#31-tạo-physical-volume)
  - [3.2 Tạo Volume group](#32-tạo-volume-group)
  - [3.3 Tạo logical volume](#33-tạo-logical-volume)
  - [3.4 Tạo filesystem trên logical volume](#34-tạo-filesystem-trên-logical-volume)
  - [3.5 Chỉnh sửa fstab để tự động mount phân vùng](#35-chỉnh-sửa-fstab-để-tự-động-mount-phân-vùng)
  - [3.6 Mở rộng logical volume](#36-mở-rộng-logical-volume)
  - [3.7 Thêm ổ cứng mở rộng logical volume](#37-thêm-ổ-cứng-mở-rộng-logical-volume)
  - [3.8 Out/thay thế 1 ổ cứng ra khỏi hệ thống LVM](#38-out-thay-thế-1-ổ-cứng-ra-khỏi-hệ-thống-LVM)
  - [3.9 Xóa logical volume, xóa volume group, xóa physical volume](#39-xóa-logical-volum-xóa-volume-group-xóa-physical-volume)
  - 
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



### 3 LVM

- LVM- Logical Volume Manaager là công nghệ quản lý ổ đĩa linh hoạt trong Linux, cho phép bạn dễ dàng:

  -Gộp nhiều ổ đĩa vật lý thành một ổ lớn (volume group)

  -Tạo ổ đĩa ảo (logical volume – LV) trên đó

  -Mở rộng hoặc thu nhỏ dung lượng ổ đĩa khi cần, mà không cần format lại

  -Di chuyển dữ liệu giữa các ổ vật lý mà không ngắt dịch vụ


- Cấu trúc cảu LVM gồm 3 lớp:
  
  - Tầng 1: PV- Physical Volume    Ổ đãi thật hoặc phân vùng
  - Tầng 2: VG- Volume Group       Gộp lại nhiều PV thành 1 khổi
  - Tầng 3: LV- Logical Volume     "Ổ đĩa ảo" dùng để mount, chứa dữ liệu

- Ưu điểm của LVM:
  
  - Mở rộng linh hoạt mà không mất dữ liệu
  - Snapshot(sao lưu nhanh)
  - Gộp nhiều ở lại làm một
  - Di chuyển dữ liệu giữa các ở dễ dàng


#### 3.1 Tạo physical volume



B1: Cài gói LVM ( hoặc cũng không cần vì nó có sẵn rồi)

```
sudo apt update
sudo apt install lvm2 -y

```


![image](https://github.com/user-attachments/assets/1e31b400-55fc-4f5d-a462-022d1316918e)

B2: Kiểm tra ở đĩa hiện có với `lsblk`

![image](https://github.com/user-attachments/assets/8ac8355e-112d-4398-9750-8d81cb196f33)


- Vì không có ổ đĩa phụ để sử dụng nên cần giải lập một ở đĩa mới bằng cách tạo ra một file và dùng nó như một ở đĩa ảo để Lab.

B3: Tạo ổ đĩa ảo từ file và dùng nó với LVM

- Tạo file ảo đại diện cho ổ đĩa: tạo file disk1.img - 3GB (count = 3072)

`sudo dd if=/dev/zero of=/root/disk1.img bs=1M count=3072`

![image](https://github.com/user-attachments/assets/79c944e2-ad06-44e5-8d38-79e689279a39)



- Gán file đó thành mooth thiết bị loop:

`sudo losetup /dev/loop10 /root/disk1.img`

Note: /dev/loop10 sẽ hoạt động như một ở đĩa thật
Kiểm tra với `lsblk` để xem có thấy hay không:

      
![image](https://github.com/user-attachments/assets/bc3831a4-cfef-492a-91db-6f6573381331)

-> đã thấy loop10

- Tạo physical Volume(PV) từ thiết bị đó

`sudo pvcreate /dev/loop10 `

![image](https://github.com/user-attachments/assets/925eb414-a325-4928-a1be-c2a5f3aa4a49)


- Kiểm tra PV đã tạo: `sudo pvs`


![image](https://github.com/user-attachments/assets/8d7ad32a-2a0f-4dfc-845d-aea36db385ac)


#### 3.2 Tạo volume group

Command: `sudo vgcreate my-vg /dev/loop10`


![image](https://github.com/user-attachments/assets/31759cd7-f5c4-4e4f-a047-bcf541879a93)

#### 3.3 Tạo Logical Volume

Command:  `sudo lvcreate -L 500M -n my-lv my-vg`

![image](https://github.com/user-attachments/assets/0d9b52de-bb1d-4d03-8e67-8d36e8a33401)


#### 3.4 Tạo filesystem trên logical volume

`sudo mkfs.ext4 /dev/my-vg/my-lv
`
my-vg là tên Volume group đã tạo, my-lv là tên logical volume đã tạo ở trên.

 ![image](https://github.com/user-attachments/assets/83d3a55f-5f85-48e1-ae3c-af4f23c430ee)


#### 3.5 Mount logical volume và chỉnh sửa fsatb để tự động mount

B1: Tạo thư mục mount point:

`sudo mkdir /mnt/lvm-test`

B2: Mount thủ công

`sudo mount /dev/my-vg/my-lv /mnt/lvm-test`


![image](https://github.com/user-attachments/assets/192ae278-d8f0-42e1-85ce-a9704f8e14a7)


B3: Thêm vào /etc/fstab

`sudo nano /etc/fstab`

B4: Thêm dòng command này vào cuối file -> lưu lại

`/dev/my-vg/my-lv   /mnt/lvm-test   ext4    defaults   0   0`

![image](https://github.com/user-attachments/assets/fdc57771-33fe-4af6-adbf-3648dcb163ae)



B5: Kiểm tra bằng sudo mount -a:

![image](https://github.com/user-attachments/assets/2ecbb1d7-8912-47c0-ae31-53c53f5eb717)

-> Không có lỗi -> thành công!





#### 3.6 Mở rộng logical volume

- Tăng kích thước cho ổ Logical Voume( + 2GB)

`sudo lvextend -L +2G /dev/my-vg/my-lv`

![image](https://github.com/user-attachments/assets/98a68175-d588-4193-a480-37c220eb4afe)


- Mở rộng hệ thống tập tin(EXT4)

`sudo resize2fs /dev/my-vg/my-lv`

![image](https://github.com/user-attachments/assets/93869b9e-42fa-466c-9248-27a08cbb59ba)



#### 3.7 Thêm ở cứng mới để mở rộng logical volume

- Tận dụng dung lượng trống chưa sử dụng trên ổ đĩa /dev/sda để làm.
`lsblk`

![image](https://github.com/user-attachments/assets/a5658592-5e91-4e0a-bde5-d18e74c1d6c5)

B1: Tạo phân vùng mới từ dung lượng trống trên /dev/sda
- Dùng `fdisk` để tạo phân vùng mới:

  `sudo fdisk /dev/sda`

![image](https://github.com/user-attachments/assets/0860b923-2fc8-4b43-b6f0-09bf50c40d8b)


- Trong giao diện `fdisk` làm theo các bước:

  Nhấn n -> Enter để tạo phân vùng mới.
  
  Nhấn Enter tiếp để chọn default cho các option. -> Thành công tạo phân vùng mới với type là     Linux File System
  
  Nhấn `w` để thoát
  
  B2: Quét lại bảng phân vùng

  `sudo partprobe`

  ![image](https://github.com/user-attachments/assets/eeb86632-3c98-4931-aa3d-8d5e55e04cc2)
  
  B3: Tạo Physical Volume từ phân vùng mới( ở đây là /dev/sda4)
  
  ![image](https://github.com/user-attachments/assets/3bbc5b17-eb3d-4aad-8ce3-25e8d103797e)
  
  `sudo pvcreate /dev/sda4`
  
  Đến đây gặp lỗi:
  ![image](https://github.com/user-attachments/assets/0f4dea8b-0868-4ecd-81db-79a83a3a91f2)
  
  -Giải pháp được đưa ra là: Thêm 1 ổ cứng 10GB vào máy ảo 

  ![image](https://github.com/user-attachments/assets/101c15ae-4f12-4b60-aeb0-7b8b508b4b31)
  

  Sau khi thêm ta làm bước tiếp theo.


  B4: Kiểm tra lại ở cứng mới trên máy bằng `lsblk`:
    - Thấy một ổ sdb đã đucợ thêm vào.
  ![image](https://github.com/user-attachments/assets/7edaef44-86e4-4445-92df-3a25836deecd)

  B5: tạo physical Volume(PV)
  
  - Với ổ mới là /dev/sdb: `pvcreate /dev/sdb`
  
  ![image](https://github.com/user-attachments/assets/d3b400e6-af43-4120-8179-f00c65951101)

  - Vẫn lỗi nên tạo một ở harđík mới thêm vào từ bên ngoài và thiết lập lại từ đầu, với các       bước tương tự như trên:

  ![image](https://github.com/user-attachments/assets/ab6eb0bc-d779-4186-ad09-eb1c446076e5)

  
  B6: mở rộng Logical Volume
  - Mở rộng mylv  thêm 2GB
    
  ```
  sudo lvextend -L +2G /dev/my-vg/mylv
  sudo resize2fs /dev/my-vg/mylv

    ```
  - Thành công mở rộng từ 5Gb lên 7GB:

  ![image](https://github.com/user-attachments/assets/17feab32-3cb9-4773-9bc3-efb3973bc5a0)


  B7: Thay thế hoặc xóa ổ cứng khỏi LVM
  
  - Ta sẽ thay thế /dev/sdb bằng 1 ổ cứng khác để chứa dữ liệu
  - Tương tự như trên, ta sẽ thêm 1 ổ cứng mới vào máy ảo.
  - sau đó dùng lệnh `lsblk` để kiểm tra ở mới
  ![image](https://github.com/user-attachments/assets/0893b283-5b2d-4404-bd1a-22d486337b66)

  - Tạo physical volume từ ở `sdc` mới:
  
  `sudo pvcreate /dev/sdc `
  
  ![image](https://github.com/user-attachments/assets/5b11b659-ac33-4c27-a8a5-0658e257b199)

  
  - Thêm vào volume group `my-vg`
  
  `sudo vgextend my-vg /dev/sdc`
  
  ![image](https://github.com/user-attachments/assets/bfff2e6d-db58-423b-9271-6376d162265a)

  - Di chuyển dữ liệu khỏi `/dev/sdb`
  
  `sudo pvmove /dev/sdb`
  Không hiểu sao vẫn lỗi:
  ![image](https://github.com/user-attachments/assets/91c512ff-4c99-498e-b515-338bb14d5c2c)

  - Đã tìm ra nguyên nhân: Do ổ mới có dung lượng thấp hơn ở cũ sdb nên xảy ra xung đột đó.
  - Đã thêm 1 ổ với dung lượng phù hơp `sdd`:

  ![image](https://github.com/user-attachments/assets/3f30afec-9738-49d6-b18e-ff93f3104dc3)

  - LÀm các bước như tạo Pv, thêm vào group, di chuyển dữ liệu:
  ![image](https://github.com/user-attachments/assets/fcc9b040-0bd7-48db-8f76-e27b8ad48c4c)

    
  - Gỡ bỏ `sdb` ra khỏi volume group:
    
  `sudo vgreduce my-vg /dev/sdb`

  ![image](https://github.com/user-attachments/assets/57870fc1-70b3-4be3-a927-2391b2645b18)

  
  - Xóa physical volume `sdb`
  
    `sudo pvremove /dev/sdb`
    
  ![image](https://github.com/user-attachments/assets/a2451e5b-912a-4ad2-9129-d3c8114ebd44)

  >>> Xóa Logical volume và volume group đang chứa trong my-vg

  - Gỡ mount nếu đang được sử dụng:

  `sudo umount /dev/my-vg/mylv
  
  ![image](https://github.com/user-attachments/assets/c22d2155-aa51-492f-a5cb-30a5b187a9ee)


  - Xóa Logical Volume:
  
  `sudo lvremove /dev/my-vg/mylv`

  - Xóa Volume Group:

  `sudo vgremove my-vg`

  ![image](https://github.com/user-attachments/assets/42ceb3b2-5093-4655-b001-507023f7806d)

  - Xóa PV Physical Volume:
  ```
  sudo pvremove /dev/sdb
  sudo pvremove /dev/sdd
  ```
  Do sdb đã xóa trước đó , nên giờ chỉ xóa sdd được

  -  Kiểm tra lại:
  ```
  sudo pvs
  sudo vgs
  sudo lvs

  ```

  Kết quả:

  ![image](https://github.com/user-attachments/assets/8d6f4b03-d7b6-4fd4-ad6e-889b2ce779c0)

  


  


  

  



  
  
  - 
  


  
  

























