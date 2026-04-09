📘 HƯỚNG DẪN THAY ĐỔI THƯ MỤC LƯU SCREENSHOT TRÊN WINDOWS
----

# 1. 🎯 Mục đích

Thay đổi vị trí mặc định lưu ảnh chụp màn hình (Screenshots) từ ổ hiện tại (D:) sang ổ DATA (E:) để:
- Tránh mất dữ liệu khi lỗi hệ điều hành
- Dễ quản lý và backup
- Chuẩn hóa theo hệ thống lưu trữ nội bộ

# 2. ⚠️ Hiện trạng
- Windows đang lưu screenshot tại:
```
D:\New folder\Screenshots
```

- Nguyên nhân:
  - Đường dẫn đã được lưu trong **Registry**
  - Không tự thay đổi theo thư mục Pictures hoặc OneDrive

# 3. 🛠️ Các bước thực hiện

**Bước 1: Mở Registry Editor**

- Nhấn:
```
Windows + R
```

- Nhập:
```
regedit
```

- Nhấn Enter

# Bước 2: Truy cập đường dẫn cấu hình

Đi tới:
```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders
```

# Bước 3: Xác định key Screenshots

Tìm key có tên:
```
{B7BEDE81-DF94-4682-A7D8-57A52620B86F}
```
👉 Đây là key quản lý thư mục Screenshots

# Bước 4: Thay đổi đường dẫn
- Double click vào key trên
- Sửa giá trị thành:

👉 Khuyến nghị:
```
E:\Screenshots
```

👉 Hoặc chuẩn hơn:
```
E:\DATA\Screenshots
```

# Bước 5: Tạo thư mục mới

Nếu chưa tồn tại, tạo folder:
```
E:\Screenshots
```

# Bước 6: Restart Explorer

Mở CMD và chạy:
```
taskkill /f /im explorer.exe
start explorer.exe
```

(hoặc restart máy)

# 4. ✅ Kiểm tra kết quả
- Nhấn:
```
Windows + PrtSc
```

👉 Kết quả đúng:

- File sẽ lưu tại:
```
E:\Screenshots
```

- Không còn lưu vào ổ D

# 5. 💡 Giải thích kỹ thuật

- Windows lưu đường dẫn Screenshot trong Registry
- Key:
```
User Shell Folders
```
quản lý các thư mục hệ thống như:

- Desktop
- Documents
- Pictures
- Screenshots

👉 Khi thay đổi path tại đây → Windows sẽ dùng path mới

# 6. 🚀 Khuyến nghị (Best Practice)

Nên chuẩn hóa theo cấu trúc:
```
E:\DATA\
 ├── Screenshots
 ├── Documents
 ├── Desktop
```

**Lợi ích:**

- Backup dễ dàng
- Không phụ thuộc ổ C
- Dễ triển khai cho nhiều máy (GPO / Script)

# 7. 🔧 (Optional) Script CMD nhanh

Có thể dùng lệnh sau để restart explorer nhanh:
```
taskkill /f /im explorer.exe && start explorer.exe
```

# 8. 📌 Lưu ý
- Không xóa key trong registry
- Chỉ sửa đường dẫn
- Đảm bảo thư mục tồn tại trước khi restart
