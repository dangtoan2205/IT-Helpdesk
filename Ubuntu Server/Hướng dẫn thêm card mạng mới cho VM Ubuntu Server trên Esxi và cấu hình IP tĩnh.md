# HƯỚNG DẪN THÊM CARD MẠNG MỚI CHO VM UBUNTU TRÊN ESXi VÀ CẤU HÌNH IP TĨNH

## 1. Thêm Network Adapter trên ESXi

### Bước 1: Shutdown VM Ubuntu

Trên Ubuntu:

```bash
sudo shutdown -h now
```

Hoặc Shutdown từ giao diện vSphere Client.

### Bước 2: Thêm Card Mạng

Truy cập:

```text
vSphere Client
→ Chọn Virtual Machine
→ Edit Settings
→ Add New Device
→ Network Adapter
```

Chọn Port Group mong muốn:

Ví dụ:

```text
VM Network
Server VLAN
Management VLAN
```

Nhấn **OK** để lưu cấu hình.

### Bước 3: Power On VM

Khởi động lại máy ảo Ubuntu.

---

# 2. Kiểm tra Ubuntu đã nhận card mạng mới hay chưa

Liệt kê toàn bộ interface:

```bash
ip a
```

Ví dụ trước khi thêm:

```text
ens33
```

Sau khi thêm:

```text
ens33
ens37
```

Trong đó:

```text
ens37
```

là card mạng mới được ESXi gán vào.

---

# 3. Kiểm tra trạng thái Link

Kiểm tra riêng card mạng mới:

```bash
ip addr show ens37
```

Hoặc:

```bash
ip link show ens37
```

### Trạng thái UP

```text
LOWER_UP
state UP
```

Card mạng đã kết nối thành công.

### Trạng thái DOWN

```text
state DOWN
```

hoặc

```text
NO-CARRIER
```

Card mạng chưa hoạt động.

---

# 4. Bật Card Mạng

Nếu interface đang DOWN:

```bash
sudo ip link set ens37 up
```

Kiểm tra lại:

```bash
ip addr show ens37
```

Nếu thấy:

```text
state UP
```

thì card mạng đã được kích hoạt.

---

# 5. Xác định Interface Chính Xác

Liệt kê tất cả interface:

```bash
ip -br addr
```

Ví dụ:

```text
lo       UNKNOWN
ens33    UP
ens37    DOWN
```

Card mới thường là interface xuất hiện sau cùng.

---

# 6. Backup File Netplan

Trước khi chỉnh sửa:

```bash
sudo cp /etc/netplan/50-cloud-init.yaml \
/etc/netplan/50-cloud-init.yaml.bak
```

---

# 7. Cấu Hình IP Tĩnh Cho Card Mạng Mới

Mở file cấu hình:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Ví dụ cấu hình:

```yaml
network:
  version: 2

  ethernets:

    ens33:
      addresses:
        - 192.168.1.200/24
      routes:
        - to: default
          via: 192.168.1.254
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1

    ens37:
      addresses:
        - 10.10.10.10/24
```

Lưu file.

---

# 8. Kiểm Tra Cú Pháp Netplan

Trước khi apply:

```bash
sudo netplan generate
```

Nếu không có lỗi tiếp tục bước tiếp theo.

---

# 9. Áp Dụng Cấu Hình

```bash
sudo netplan apply
```

Kiểm tra trạng thái:

```bash
ip a
```

---

# 10. Kiểm Tra Địa Chỉ IP

```bash
ip addr show ens37
```

Kết quả mong muốn:

```text
inet 10.10.10.10/24
```

---

# 11. Kiểm Tra Routing

```bash
ip route
```

Ví dụ:

```text
default via 192.168.1.254 dev ens33
192.168.1.0/24 dev ens33
10.10.10.0/24 dev ens37
```

Lưu ý:

* Chỉ nên có 1 default gateway.
* Không khai báo default gateway trên nhiều interface nếu không có policy routing.

---

# 12. Kiểm Tra Kết Nối

Ping chính card mạng:

```bash
ping -I ens37 10.10.10.1
```

Ping Gateway:

```bash
ping 192.168.1.254
```

Ping Internet:

```bash
ping 8.8.8.8
```

Kiểm tra DNS:

```bash
ping google.com
```

---

# 13. Xử Lý Sự Cố Thường Gặp

## Card mạng không xuất hiện

Kiểm tra:

```bash
lspci | grep Ethernet
```

Hoặc:

```bash
ip a
```

Shutdown VM và kiểm tra lại cấu hình Network Adapter trên ESXi.

---

## Interface xuất hiện nhưng NO-CARRIER

Kiểm tra:

```bash
ethtool ens37
```

Nguyên nhân thường gặp:

* Port Group ESXi sai VLAN.
* vSwitch chưa kết nối uplink.
* VLAN chưa được trunk từ switch vật lý.

---

## Netplan báo lỗi

Kiểm tra:

```bash
sudo netplan generate
```

Xem lại:

* Khoảng trắng (indent).
* Thiếu dấu ":".
* Sai tên interface.

---

# 14. Kiểm Tra Sau Khi Reboot

Khởi động lại:

```bash
sudo reboot
```

Sau khi máy lên:

```bash
ip a
ip route
```

Đảm bảo:

* Interface ens37 vẫn tồn tại.
* Địa chỉ IP được gán chính xác.
* Gateway hoạt động bình thường.
* Ping nội bộ và Internet thành công.
