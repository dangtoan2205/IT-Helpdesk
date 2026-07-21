Hướng dẫn copy file từ trong VM ra ngoài máy thật khi không kéo file trực tiếp từ VM ra bên ngoài.md
---

## Cách 1: Shared Folder (Khuyến nghị)

Tắt VM.

VM → Settings → Options → Shared Folders.

Chọn Always enabled.

Add một thư mục trên máy Windows, ví dụ:
```
D:\VMShare
```
Trong Ubuntu sẽ xuất hiện tại:
```
ls /mnt/hgfs
```

Ví dụ:
```
VMShare
```

Sau đó copy:
```
cp ~/Downloads/Turbo_X_2.5.0.exe /mnt/hgfs/VMShare/
```

Hoặc:
```
cp ~/Downloads/*.iso /mnt/hgfs/VMShare/
```

> **Nếu không thấy thư mục ISO xuất hiện tại VM Ubuntu tại thư mục `"ls /mnt/hgfs"` nữa thì chọn lựa chọn `"Enable until next power off or suspend"` để bật lại.
> Hoặc thử chuyển đổi từ Enable -> Disable -> chọn OK xong rồi chuyển Disable -> Enable -> OK (Cách này được)
> Khi tích chọn xong thì vào VM Ubuntu kiểm tra xem thư mục ISO đã có chưa `"ls /mnt/hgfs"`.
> Có thể thực hiện ngay cả khi VM Ubuntu đang hoạt động.**
<img width="756" height="423" alt="image" src="https://github.com/user-attachments/assets/c8a1c744-02fb-4396-af82-e1e145097219" />

----

## Cách 2: Dùng SCP (Nếu Ubuntu có SSH)

Trên Windows cài WinSCP hoặc dùng lệnh:
```
scp ubuntu@192.168.1.100:/home/ubuntu/Downloads/*.iso D:\Download\
```
----

## Cách 3: USB

Cắm USB vào.

Trong VMware:
```
VM
→ Removable Devices
→ USB
→ Connect
```

Ubuntu sẽ mount USB.

Ví dụ:
```
lsblk
```
Giả sử USB là:
```
/media/ubuntu/USB
```
Copy:
```
cp ~/Downloads/*.iso /media/ubuntu/USB/
```
----
## Cách 4: Dùng File Browser qua mạng

Nếu Ubuntu và Windows cùng mạng:

Ubuntu:
```
sudo apt install samba
```
hoặc
```
python3 -m http.server 8000
```
Sau đó trên Windows mở:
```
http://IP-Ubuntu:8000
```
Tải file về.
