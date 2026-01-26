TẮT TÍNH NĂNG TỰ ĐỘNG CẬP NHẬT VÀ TỰ ĐỘNG TẢI BẢN CẬP NHẬT TRÊN WINDOWS
----

# 1. Mục đích

Ngăn Windows tự động:
- Kiểm tra cập nhật
- Tải bản cập nhật về máy
- Cài đặt bản cập nhật ngoài ý muốn </br>
Áp dụng cho Windows 10 và Windows 11 (đặc biệt bản Home).

# 2. Phạm vi áp dụng
- Windows 10 / Windows 11 (Home, Pro)
- Người dùng có quyền Administrator

# 3. Phương pháp: Tắt bằng Registry (ổn định, không bị tự bật lại)
## 3.1. Mở Registry Editor

1. Nhấn **Win + R**

2. Gõ: `regedit`

3. Nhấn **Enter**

4. Chọn **Yes** nếu có cảnh báo UAC

## 3.2. Tạo cây khóa chính sách Windows Update

Đi tới:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows
```
Nếu chưa có:
- Chuột phải vào **Windows**
- Chọn **New → Key**
- Đặt tên: `WindowsUpdate`

Tiếp tục:

- Chuột phải vào **WindowsUpdate**
- Chọn **New → Key**
- Đặt tên: `AU`

Sau khi tạo xong, đường dẫn đầy đủ sẽ là:
```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU
```

## 3.3. Tạo khóa tắt cập nhật tự động

Trong thư mục **AU**:

1. Chuột phải vùng trống bên phải

2. Chọn **New → DWORD (32-bit) Value**

3. Đặt tên: `NoAutoUpdate`

4. Double-click vào khóa vừa tạo

5. Nhập:

 - Value data: **1**

 - Base: **Hexadecimal**

6. Nhấn **OK**

## 3.4. Khởi động lại hệ thống

Restart máy để chính sách có hiệu lực.

# 4. Kết quả đạt được

Sau khi cấu hình:
- Windows không còn tự động kiểm tra update
- Không tự động tải bản vá về máy
- Không tự động cài đặt nền
- Không tự bật lại sau reboot

# 5. Khôi phục lại cập nhật (khi cần)

Thực hiện một trong hai cách:

**Cách 1:**

Xóa khóa `NoAutoUpdate`

**Cách 2:**

Đổi giá trị `NoAutoUpdate` từ **1 → 0**

Sau đó restart máy.
