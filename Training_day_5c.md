# Công ty Nhân Hòa - Kế hoạch đào tạo 7 ngày (Ngày 5c Mục 22)

## Mục lục
### Firewall - Pfsense
  

- [1 Triển khai pfsense](#1-triển-khai-pfsense)

- [2 NAT Local Internet](#2-nat-local-internet)

- [3 Firewall Rule ](#3-firewall-rule)

- [4 DHCP](#4-dhcp)

- [5 Setup OpenVPN với Pfsense](#5-setup-openvpn-với-pfsense)
  
  -[5.1 OpenVPN mode tab](#5.1-openvpn-mode-tab)
  -[5.2 OpenVPN mode Tun](#5.2-openvpn-mode-tun)
  

### 1 Triển khai pfsense

- pfSense là một phần mềm mã nguồn mở, miễn phí, được sửu dụng để triển khai và quản lý các firewall và router. Nó cung cấp nhiều tính năng như VPN,NAT,DHCP và nhiều cấu hình bảo mật mạng khác

- Tải pfSense ISO từ trang chủ pfSense.
  khi vào trang chủ chính thức của pfsense, ta không thể dowload chính thức được nhanh như trước   nữa vì cần phải order qua netgate:

![image](https://github.com/user-attachments/assets/80343769-12b6-485d-bf7c-2f0bf787f779)

![image](https://github.com/user-attachments/assets/67683bb5-76c5-4e77-ac33-1d67a1ab02b8)

nên phải tìm 1 trang web không chính thức để dowload- > thành công tải file iso pfsense 2.7.2

- Tạo máy ảo mới trong VMware:

  ![image](https://github.com/user-attachments/assets/9a39b815-152f-43f4-b69c-9c863c4de3b1)

- Accept để cài đặt:

  ![image](https://github.com/user-attachments/assets/5f5ea862-9ac3-41f5-bfe2-574696471381)

- Nhấn Enter( Ok) để cài đặt:

![image](https://github.com/user-attachments/assets/9d21ab69-0282-4597-bdd2-7bfd71e3bb3b)

- tiếp theo mục disk: chọn auto rồi Enter(ok)

![image](https://github.com/user-attachments/assets/3d545352-9763-4fc8-bf8a-df2dd36ad686)

- Nhấn Enter(select) để tiếp tục:

![image](https://github.com/user-attachments/assets/cedc8d75-37c6-43c1-86f0-048cb5a1cb55)

- Nhấn Enter(OK) để tiếp tục:

![image](https://github.com/user-attachments/assets/24a19909-b92a-4b19-a452-b0b9acadf31c)

- Chọn Disk cài đặt và nhân Enter(OK)
  
![image](https://github.com/user-attachments/assets/b16dbbe4-f686-4b56-9d35-ac32227307ae)

- Nhấn Enter(Yes)

![image](https://github.com/user-attachments/assets/a5e4cab8-675e-4eee-8767-549ed654e3e9)

- Đợi cho PFsense cài nốt:

  ![image](https://github.com/user-attachments/assets/a80ee43c-2456-4441-ba5a-b85d94491e88)

  ![image](https://github.com/user-attachments/assets/c675447f-6817-4970-81d6-ecc7ca9971b0)


- Chọn Reboot (Enter)
  
  ![image](https://github.com/user-attachments/assets/5e124163-31c4-4f7f-b529-ee6f1195e0d6)

- Nhập name cho interface WAN:

![image](https://github.com/user-attachments/assets/d5fac6a4-5b5b-43fa-a706-1612debe91d2)

- Nhập name cho interface Lan -> Nhấn Enter:

![image](https://github.com/user-attachments/assets/2f88eb9f-5780-4f44-b4f3-c952b8c101f9)

- Nhấn y để confirm:

![image](https://github.com/user-attachments/assets/f635d56e-e861-4092-8a65-42ba76a66fb2)




  








  
