Khắc phục lỗi USB nhận trong Windows nhưng không hiển thị trên File Explorer
----
# 1. Mô tả lỗi

Hiện tượng

Khi cắm USB/HDD Box vào máy tính:

- Windows phát ra âm thanh kết nối USB.
- Góc phải Taskbar xuất hiện biểu tượng **Safely Remove Hardware** và hiển thị:
```
Eject Ugreen Storage Device
```

Trong **Disk Management** vẫn nhìn thấy ổ đĩa.
Nhưng trong **This PC (File Explorer)** lại không xuất hiện ổ USB.

Ví dụ:

```
Disk 2
├── HDD-BOOT (FAT32) 8.79 GB
└── Partition 229.68 GB
```

Trong Disk Management chỉ hiển thị:
```
HDD-BOOT
```
thay vì
```
HDD-BOOT (G:)
```

# 2. Nguyên nhân

Nguyên nhân phổ biến nhất là:

- Phân vùng chưa được gán Drive Letter (ký tự ổ đĩa).

- Windows vẫn nhận được thiết bị nhưng vì không có ký tự ổ đĩa nên File Explorer sẽ không hiển thị.

Có thể kiểm tra bằng:
```
Disk Management
```
Nếu phân vùng chỉ hiển thị:
```
HDD-BOOT
```
thì nghĩa là chưa có Drive Letter.

Nếu đã được gán sẽ hiển thị:
```
HDD-BOOT (G:)
```

# 3. Cách kiểm tra

## Bước 1

Mở:
```
Computer Management

↓

Storage

↓

Disk Management
```
Kiểm tra xem USB có xuất hiện hay không.

Ví dụ:
```
Disk 2
```
Nếu thấy Disk 2 nghĩa là Windows đã nhận USB.

## Bước 2

Kiểm tra xem phân vùng có Drive Letter không.

Ví dụ:

Đúng:

```
HDD-BOOT (G:)
```
Sai:

```
HDD-BOOT
```
# 4. Cách khắc phục

## Cách 1: Gán Drive Letter bằng giao diện

Chuột phải vào phân vùng
```
HDD-BOOT

↓

Change Drive Letter and Paths...

↓

Add...

↓

Chọn ký tự

Ví dụ:

G:

↓

OK
```

Sau vài giây mở lại

This PC

Ổ USB sẽ xuất hiện.

## Cách 2: Gán bằng DiskPart

Mở CMD với quyền Administrator
```
diskpart
```
Liệt kê volume
```
list volume
```
Ví dụ
```
Volume 5
FAT32
8.79 GB
```
Chọn volume
```
select volume 5
```
Gán ký tự
```
assign letter=G
```
Thoát
```
exit
```

# 5. Muốn ẩn USB khỏi File Explorer

Nếu không muốn người dùng nhìn thấy ổ USB nhưng vẫn muốn Windows nhận thiết bị.

## Cách 1:
```
Chuột phải

HDD-BOOT

↓

Change Drive Letter and Paths...

↓

Chọn

G:

↓

Remove

↓

Yes
```
Sau đó ổ sẽ biến mất khỏi
```
This PC
```
nhưng vẫn xuất hiện trong
```
Disk Management
```

## Cách 2: Thực hiện bằng DiskPart

Liệt kê Volume
```
diskpart
list volume
```
Chọn Volume
```
select volume 5
```
Xóa Drive Letter
```
remove letter=G
```
Thoát
```
exit
```

> Lưu ý

Không chọn:
```
Delete Volume
```
vì thao tác này sẽ xóa toàn bộ dữ liệu trên phân vùng.

Chỉ sử dụng:
```
Add

hoặc

Remove
```

để thêm hoặc bỏ ký tự ổ đĩa.

Cách phân biệt các trường hợp: 

| Hiện tượng                                            | Nguyên nhân                      | Cách xử lý                            |
| ----------------------------------------------------- | -------------------------------- | ------------------------------------- |
| Không có âm thanh khi cắm USB                         | Cổng USB hoặc USB hỏng           | Kiểm tra cổng, cáp, USB               |
| Có "Eject USB" nhưng không thấy trong Disk Management | Driver hoặc thiết bị lưu trữ lỗi | Kiểm tra Device Manager, thử máy khác |
| Có trong Disk Management nhưng không có trong This PC | Chưa gán Drive Letter            | Add Drive Letter                      |
| Có trong This PC nhưng không mở được                  | Hệ thống file lỗi                | Chạy `chkdsk`, kiểm tra phân vùng     |
| Không thấy trong cả Disk Management và This PC        | Windows chưa nhận USB            | Kiểm tra phần cứng hoặc driver        |

# 6. Kết luận

Trong trường hợp này:

- Windows đã nhận thiết bị USB (Ugreen Storage Device).
- Disk Management đã nhận diện được ổ Disk 2.
- Phân vùng HDD-BOOT không hiển thị trong File Explorer vì chưa được gán Drive Letter.
- Chỉ cần Add Drive Letter là ổ sẽ xuất hiện trong This PC.
- Nếu muốn ẩn lại, chỉ cần Remove Drive Letter, không cần xóa phân vùng hoặc format ổ đĩa.
