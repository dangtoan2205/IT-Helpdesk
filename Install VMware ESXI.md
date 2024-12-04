Cài đặt máy chủ ESXi
--- 

## Bước 1: Cài đặt máy ảo
-	Tạo một máy ảo mới
New -> Custom -> Next -> I will install the operating system later. -> Next
VMware ESX (VMware ESXi 6.x)   Next
Chọn nơi lưu trữ 
Chọn Processors 
 ![image](https://github.com/user-attachments/assets/8c1ddc7a-6cc9-467c-a8af-aa1d78c58fee)

->	Next
Chọn dung lượng RAM
![image](https://github.com/user-attachments/assets/ef236505-07c7-477d-97ea-2349810dd68d)

![image](https://github.com/user-attachments/assets/59720a0c-3731-4018-a67c-e4c805b606b1)

![image](https://github.com/user-attachments/assets/602a5813-777e-4a90-8a20-de338c278287)

![image](https://github.com/user-attachments/assets/cfc92169-a9c9-47f4-bce7-9ef7bb4781c4)

![image](https://github.com/user-attachments/assets/012f7e68-5c01-46ae-b92d-1c86b9a1f83f)


Bước 2: Thiết lập tùy chỉnh cho máy ảo
-	Edit virtual machine settings
Thêm 3 Network Adapter mới và để là Bridged
 
Thêm 1 Hard Disk (SCSI) mới
CD/DVD (IDE)  Use ISO image file:

 

Bước 3: Cấu hình đặt ip tĩnh VMware ESXi 
 
Enter
 
F11
Chọn ổ đĩa cài
 
Chọn bàn phím
 
Điền mật khẩu
 
Cài đặt 
 

Enter Reboot
 
Giao diện chính
Bấm F2 để đăng nhập  
Đặt ip tĩnh cho máy chủ
 

Configure Management Network  Ipv4 Configuration
 
 

 

ESC  Y (để khởi động lại)
Bước 4: Cấu hình trên web ESXi
Vào trình duyệt web với địa chỉ ip tĩnh đặt cho ESXi 192.168.80.100
 


 
Cài firewall pfSense trên máy chủ ESXi 
 
Bước 1: Kiểm tra tất cả các card mạng hiện có
Networking  Virtual switches  Physical NICs
 
Bước 2: Thêm card mạng vào Virtual switches
Networking  Virtual switches  Add standard virtual switch 
 
Bước 3: Thêm Virtual switch vào Port Group
Networking  Virtual switches  Port groups  Add port group
 
Tạo tên mới là Server-LAN và thêm vSwitch1 vừa tạo vào.

Bước 4: Thêm ổ đĩa mới
Storage  New datastore
 
Bước 5: Tạo máy ảo pfsense
Virtual Machines  Create / Register VM  
Select creation type  Next
 
 
 
 

Bước 6: Cấu hình pfSense
 
	Accept
 
	OK
 
	OK

 

 
 
Bấm phím cách để lựa chọn  OK
 

 
Chọn Reboot

 
Bước 7: Cấu hình đặt ip v4 tĩnh cho máy ảo pfsense
Chọn  2 
 

Set ip tĩnh cho LAN
 
 
 


Bước 8: Cấu hình trên giao diện pfSense
Truy cập địac chỉ ip tĩnh cho pfSense 192.168.1.101
  
Đăng nhập:
User name: admin
Password: pfsense

 
Accept
 
Next
 
Next
 
Next  
Next
 
Đặt ip tĩnh cho cổng WAN : DHCP  Static
 
Đặt ip tĩnh cho cổng WAN là 192.168.1.2
Subnet Mask là 24
Upstream Gateway là ip gateway để cổng WAN này ra internet 192.168.1.1 đây là ip LAN của Router internet
 
Bỏ chọn 2 lựa chọn này  Next
 
Cấu hình địac chỉ ip cho LAN
 
Nhập mật khẩu cho Admin
 
Reload

Vào lại bằng địa chỉ ip vừa đặt tĩnh cho pfsense 192.168.10.1
Đăng nhập lại với mật khẩu vừa tạo
User name: admin
Password: admin





