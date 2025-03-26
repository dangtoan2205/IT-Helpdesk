Install and config TrueNAS
----------------


## 1. Tạo usb boot 

1/ Tải Install and config TrueNAS

Truy cập link sau để tải về TrueNAS scale: 

```
https://www.truenas.com/download-truenas-scale/
```
![image](https://github.com/user-attachments/assets/ce6e76a4-67f2-4512-956d-fc2bd6d22082)

Chọn `No Thank you, I have already signed up.`

![image](https://github.com/user-attachments/assets/8d259f6c-ee0b-484b-a212-645ece25ca2a)

Chọn `Download STABLE`

2/ Cài usb boot Rufus

![image](https://github.com/user-attachments/assets/fa6c2431-8a16-4be6-8a84-bca3b8d672b3)

3/ Vào BIOS thiết lập BOOT chuẩn LEGACY, tắt BOOT SECURE

## Cài đặt TrueNAS

![image](https://github.com/user-attachments/assets/c2e5247c-93b0-4977-8537-f7c66c871d01)


![image](https://github.com/user-attachments/assets/568f0cf5-3148-4ac0-acff-1794e3c118a8)

-> Install/Upgrade -> OK

![image](https://github.com/user-attachments/assets/e8956a2e-adfc-4c8b-b14e-fb6b79db5569)

-> Chọn ổ cứng muốn cài TrueNAS lên (bấm SPACE để chọn) -> OK

![image](https://github.com/user-attachments/assets/0eaadc55-66ae-4531-ad42-3aee820132ee)

-> Chọn OK

![image](https://github.com/user-attachments/assets/0bace4bf-b0ac-43de-aa80-8cfec5b8c7e5)

-> 2 Configure using Web UI -> OK 

![image](https://github.com/user-attachments/assets/15b2d2c2-0cf7-42a1-b480-780eefd49229)

Chọn NO

![image](https://github.com/user-attachments/assets/0ff2ad18-3478-4d7b-8f22-9d045a48a4c0)

Đến bước này thì rút usb boot ra rồi chọn OK

![image](https://github.com/user-attachments/assets/ac31598a-58e9-44b7-955f-f494380c43e7)

Chọn Reboot System để khởi động vào OS TrueNAS

------------------------

FIX lỗi

## Lỗi 1:

![image](https://github.com/user-attachments/assets/b6d71359-d0b4-474d-af97-e32b7d7a3857)

```
command:
zpool import -N 'boot-pool'
message: cannot import 'boot-pool': more than one matching pool
error: 1
failed to import pool 'boot-pool'.
manually import the pool and exit.
```

Nếu thấy lỗi này xuất hiện thì sửa như sau:

Khắc phục thủ công trong initramfs

Hệ thống đã rơi vào môi trường initramfs, và thông báo yêu cầu bạn "manually import the pool and exit". Tôi sẽ hướng dẫn bạn nhập boot-pool thủ công.

1/ Kiểm tra các ZFS pool có sẵn:

- Tại prompt (initramfs), gõ:

```
zpool import
```

- Lệnh này sẽ liệt kê tất cả các ZFS pool có sẵn trên hệ thống. Bạn sẽ thấy danh sách các pool, bao gồm nhiều pool có tên boot-pool. Ví dụ:

```
pool: boot-pool
id: 123456789
state: ONLINE
...
pool: boot-pool
id: 987654321
state: ONLINE
```

- Ghi lại ID của các pool có tên boot-pool.

2/ Nhập pool đúng bằng ID:

- Để tránh xung đột, bạn cần nhập pool bằng ID thay vì tên. Chọn ID của pool trên ổ cứng (thường là pool mới nhất bạn vừa cài đặt). Ví dụ, nếu ID của pool mong muốn là `123456789`, gõ:

```
zpool import -f 123456789
```

- Tham số `-f` (force) ép nhập pool ngay cả khi có xung đột.

3/ Kiểm tra pool đã nhập:

```
zpool list
```

- Nếu bạn thấy `boot-pool` trong danh sách, pool đã được nhập thành công.

4/ Thoát khỏi initramfs:

```
exit
```

- Hệ thống sẽ tiếp tục quá trình khởi động. Nếu thành công, bạn sẽ vào được giao diện TrueNAS.

------------
Xóa hoặc đổi tên pool trùng lặp (nếu cần)
------------

Nếu lỗi vẫn xảy ra sau khi tháo USB, có thể có nhiều boot-pool trên ổ cứng (do cài đặt nhiều lần). Bạn cần xóa hoặc đổi tên pool trùng lặp.

1/ Boot lại từ USB cài đặt:

- Cắm USB TrueNAS vào máy.
- Khởi động lại và nhấn F12 để vào Boot Menu.
- Chọn USB dưới mục UEFI BOOT để boot từ USB.

2/ Vào chế độ cài đặt TrueNAS:

- Khi giao diện TrueNAS hiện ra, chọn tùy chọn Shell hoặc Live CD (thay vì cài đặt lại).

3/ Kiểm tra các pool:

```
zpool import
```

- Xác định các pool có tên boot-pool và ghi lại ID của chúng.

4/ Xóa pool không cần thiết:

- Giả sử bạn muốn giữ pool có ID 123456789 (pool trên ổ cứng) và xóa pool có ID 987654321 (pool cũ hoặc trên USB), gõ:

```
zpool destroy 987654321
```

- Lưu ý: Hãy chắc chắn bạn chọn đúng pool để xóa, vì lệnh này sẽ xóa toàn bộ dữ liệu trên pool đó.

5/ Nhập lại pool chính:

```
zpool import -f 123456789
```

- Đảm bảo pool được nhập thành công (zpool list).

6/ Khởi động lại:

Thoát khỏi shell và khởi động lại máy (tháo USB ra).



## Cấu hình thiết lập bên trên TrueNAS UI

![image](https://github.com/user-attachments/assets/cf7d79aa-5372-47bf-a869-6f5ab5aa119f)

Thiết lập Password

![image](https://github.com/user-attachments/assets/59d78fa8-80d4-43ca-9ef9-9d06b7c95c59)

Kiểm tra ổ cứng có trên máy

Storage -> Disk 

![image](https://github.com/user-attachments/assets/b9b29c23-2047-4f85-bf49-982c8cf37a4b)

Cần xóa dữ liệu cũ trên các ổ cứng trên

![image](https://github.com/user-attachments/assets/9635a5e6-87a6-4fa9-92ea-52e5b6a2aa61)

Tích chọn các ổ cứng cần xóa

Chọn Wipe 

![image](https://github.com/user-attachments/assets/41c329be-3936-47fa-a308-91aa0411b699)

Chọn Wipe

![image](https://github.com/user-attachments/assets/72c93328-3e79-44ed-b8e4-5aede89ac57f)

Tích chọn Confirm -> Continue

![image](https://github.com/user-attachments/assets/f1f8b480-c284-44cd-936e-a5b89f3eb0f1)

![image](https://github.com/user-attachments/assets/cbebc425-bc70-4716-8c8f-b398c0a99c58)

Thành công

## Tạo ổ cứng mới 

![image](https://github.com/user-attachments/assets/c7de8fb8-72e3-40a9-b7d4-72d280bd92c0)

Storage -> Create pool

![image](https://github.com/user-attachments/assets/fa307c8e-8a55-49fe-93b8-77425b7bc2f8)

-> Next

![image](https://github.com/user-attachments/assets/b32250a7-ff9e-4773-92ba-3e026074a678)

-> Next

![image](https://github.com/user-attachments/assets/6fb37060-c566-467f-b12f-48e57de529f0)

-> Next

![image](https://github.com/user-attachments/assets/449373aa-972b-4ac8-a382-27beafba0c6c)

-> Next

![image](https://github.com/user-attachments/assets/9448c656-d70d-49ed-a993-65d053b20d7b)

-> Next

![image](https://github.com/user-attachments/assets/72cb793d-0a74-45eb-b8b6-6be1d773db5f)

-> Next

![image](https://github.com/user-attachments/assets/76207e3a-584a-42ef-9bd2-1a8e7301a7fb)

-> Next

![image](https://github.com/user-attachments/assets/265fc972-eb13-444a-adb4-c69a09eb0298)

-> Create Pool

![image](https://github.com/user-attachments/assets/132e0543-ec6e-4a61-8f81-cc6400cfcf69)

Tích chọn Confirm -> Continue

## Thêm tài khoản mới





























