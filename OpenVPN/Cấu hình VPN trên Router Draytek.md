Hướng dẫn cấu hình VPN trên Router Draytek Vigor 2962
-------------


## Step 1: Đăng nhập vào giao diện quản lý của router

1/ Kết nối vào mạng LAN của công ty.

2/ Mở trình duyệt và truy cập địa chỉ: http://192.168.60.1

3/ Đăng nhập bằng tài khoản quản trị của router.

## Step 3: Kích hoạt dịch vụ VPN trên router

1/ Vào VPN and Remote Access > Remote Access Control.

2/ Bật các tùy chọn sau:
```
✔️ Enable PPTP VPN Service
✔️ Enable L2TP with IPsec Service
✔️ Enable SSL VPN Service
```
3/ Nhấn OK để lưu cấu hình.

![image](https://github.com/user-attachments/assets/0494ecf7-01c8-498e-ba14-c173c346460b)

## Step 3: Tạo tài khoản VPN cho người dùng

1/ Vào VPN and Remote Access > Remote Dial-in User.

2/ Nhấn Add để thêm tài khoản VPN mới:

```
Username: vpnuser (tùy chọn)
Password: your_password (mật khẩu VPN)
```

```
3/ Allowed Dial-in Type:
✔️ PPTP
✔️ L2TP with IPsec
✔️ SSL VPN
```

Nhấn OK để lưu cấu hình.

![image](https://github.com/user-attachments/assets/7c9d5bdc-f47f-4b7a-ab34-2dc26fc39ae0)


## Step 4: Cấu hình L2TP/IPsec (tùy chọn khuyến nghị)
1/ Vào VPN and Remote Access > IPsec General Setup.

2/ Thiết lập:

IKE Authentication Method: Pre-shared key
Pre-shared key: your_psk (nhớ mã này để dùng khi thiết lập trên máy tính).

3/ Nhấn OK để lưu cấu hình.

![image](https://github.com/user-attachments/assets/3a07fe89-567f-4684-925d-bcdeeadd8391)

![image](https://github.com/user-attachments/assets/3eb61a14-cd5f-4166-9e68-6c10ea653000)

## Step 5: Tải DrayTek Smart VPN Client từ Google

![image](https://github.com/user-attachments/assets/c82fa426-d173-46cc-bbd7-667fe8930088)


-------------------
Cấu hình OpenVPN trên DrayTek Vigor 2962
-------------------

## Bước 1: Kích hoạt dịch vụ OpenVPN

1/ Đăng nhập vào giao diện quản lý của router.

- Địa chỉ: http://192.168.60.1

2/ Vào VPN and Remote Access > Remote Access Control.

3. Bật tùy chọn Enable OpenVPN Service.

4. Nhấn OK để lưu cấu hình.

## Bước 2: Tạo tài khoản OpenVPN

1/ Vào VPN and Remote Access > Remote Dial-in User.

2/ Nhấn Add để tạo tài khoản OpenVPN:

Username: vpnuser

Password: your_password

Allowed Dial-in Type: ✔️ OpenVPN

3/ Nhấn OK để lưu.

## Bước 3: Cấu hình OpenVPN Server

1/ Vào VPN and Remote Access > OpenVPN.

2/ Cấu hình các thông số sau:

![image](https://github.com/user-attachments/assets/63841d74-979a-46d8-b881-c6992199f089)

3/ Nhấn OK để lưu cấu hình.

## Bước 4: Tải file cấu hình OpenVPN (.ovpn)

1/ Vào VPN and Remote Access > OpenVPN > Export OpenVPN Client Setup.

2/ Tải file .ovpn về máy tính.


-------------
Khắc phục sự cố OpenVPN
-------------

1/ Kiểm tra cổng OpenVPN (1194)
 - Đảm bảo cổng 1194 được mở trên router.
 - Nếu cần, vào NAT > Port Redirection để tạo rule mở cổng:

![image](https://github.com/user-attachments/assets/15ae1878-867f-4781-824b-3d03397ab5c9)

2/ Kiểm tra Firewall của router

Vào Firewall > General Setup và đảm bảo dịch vụ OpenVPN không bị chặn.

![image](https://github.com/user-attachments/assets/ca3e78a1-3e83-42a3-b415-8cbad627dc84)
