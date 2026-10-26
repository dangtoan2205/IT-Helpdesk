# HƯỚNG DẪN CẤP INTERNET CHO UBUNTU SERVER BẰNG USB DCOM

## 1. Kiểm tra USB Dcom đã được hệ điều hành nhận chưa

Cắm USB Dcom vào server và thực hiện:

```bash
lsusb
```

Kiểm tra xem có xuất hiện thiết bị USB Dcom hay không.

---

## 2. Xác định interface mạng của Dcom

Liệt kê các card mạng:

```bash
ip a
```

Tìm interface mới xuất hiện sau khi cắm Dcom, ví dụ:

```text
usb0
wwan0
enxxxxxxxxxxxx
```

---

## 3. Bật interface mạng

Ví dụ interface là `usb0`:

```bash
sudo ip link set usb0 up
```

Kiểm tra trạng thái:

```bash
ip a show usb0
```

---

## 4. Nhận địa chỉ IP từ Dcom

Xin IP động từ DHCP:

```bash
sudo dhclient usb0
```

Kiểm tra địa chỉ IP:

```bash
ip a show usb0
```

Kiểm tra gateway:

```bash
ip route
```

---

## 5. Kiểm tra kết nối Internet

Ping địa chỉ IP công cộng:

```bash
ping 8.8.8.8
```

Kiểm tra phân giải DNS:

```bash
ping google.com
```

Nếu ping thành công là server đã có Internet.

---

## 6. Cấu hình tự động kết nối sau khi khởi động

Mở file Netplan:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Ví dụ interface Dcom là `usb0`:

```yaml
network:
  version: 2
  ethernets:
    usb0:
      dhcp4: true
```

Áp dụng cấu hình:

```bash
sudo netplan generate
sudo netplan apply
```

---

## 7. Kiểm tra lại sau khi reboot

Khởi động lại server:

```bash
sudo reboot
```

Sau khi khởi động:

```bash
ip a
ip route
ping 8.8.8.8
```

Đảm bảo interface Dcom tự động nhận IP và truy cập Internet bình thường.

---

## Lưu ý

* Nếu `lsusb` không thấy Dcom, thử đổi sang cổng USB khác.
* Nếu không xuất hiện interface mạng mới sau khi cắm Dcom, thiết bị có thể chưa được Ubuntu hỗ trợ.
* Với Dell PowerEdge R630/R730 hoặc Fujitsu RX2540, nên ưu tiên cắm Dcom vào cổng USB phía sau máy chủ.
* Để cài đặt hệ điều hành hoặc cập nhật gói phần mềm, nên sử dụng LAN hoặc USB Tethering từ điện thoại nếu có thể.
