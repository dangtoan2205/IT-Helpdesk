## 1. Dựng Ubuntu Server (có thể là VPS hoặc máy chủ vật lý).

## 2. Cập nhật hệ thống:

```
sudo apt update && sudo apt upgrade -y
```

## 3. Tải và chạy script:

```
wget https://raw.githubusercontent.com/Nyr/openvpn-install/master/openvpn-install.sh
chmod +x openvpn-install.sh
sudo ./openvpn-install.sh
```

## 4. Xác minh thông tin đầu vào trong script

- IP Public: Script tự động nhận diện IP public của bạn là 113.190.252.197.

- Chọn giao thức: Bạn chọn UDP (1), đây là lựa chọn tốt vì UDP thường nhanh hơn TCP.

- Chọn port: Bạn để mặc định là 1194, đúng với cổng chuẩn của OpenVPN.

- Chọn DNS: Bạn chọn tùy chọn 1 (Sử dụng DNS hệ thống).

- Tạo user: Bạn tạo user có tên toandn

## 5. Kiểm tra sau khi cài đặt

Sau khi cài đặt xong, kiểm tra các dịch vụ OpenVPN có đang chạy không:

```
sudo systemctl status openvpn
```

Hoặc kiểm tra OpenVPN có đang lắng nghe trên port `1194` không:

```
sudo netstat -tulnp | grep 1194
```

Nếu OpenVPN đang chạy, bạn sẽ thấy output hiển thị openvpn lắng nghe trên cổng 1194.

## 6. Sao chép file .ovpn này từ server về máy tính hoặc điện thoại để kết nối VPN. Nếu đang dùng máy tính để kết nối SSH, có thể sử dụng scp để sao chép file về máy:

```
scp root@your-server-ip:/root/toandn.ovpn ./
```

Thay `your-server-ip` bằng địa chỉ IP của server.


Mở cổng trên firewall nếu cần
---------------

Nếu server có firewall (`ufw`), bạn cần mở cổng `1194` để cho phép kết nối VPN:

```
sudo ufw allow 1194/udp
sudo ufw reload
```

Nếu sử dụng `iptables`, bạn cần thêm quy tắc NAT:

```
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE
sudo iptables-save > /etc/iptables.rules
```












