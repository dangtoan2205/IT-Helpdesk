TÀI LIỆU HƯỚNG DẪN SAO CHÉP DỮ LIỆU GIỮA HAI TABLET ANDROID BẰNG ADB
----

**Áp dụng**: Samsung Galaxy Tab và các thiết bị Android hỗ trợ USB Debugging.

**Công cụ**: Android SDK Platform-Tools (ADB).
ADB
**Mục đích**: Sao chép toàn bộ file và thư mục con trong `Bộ nhớ trong → Download` từ Tablet 1 về laptop, sau đó chuyển sang Tablet 2.

## 1. Chuẩn bị
1. Laptop đã cài đặt Android SDK Platform-Tools.
2. Hai tablet đã bật Developer Options → USB Debugging.
3. Cáp USB hỗ trợ truyền dữ liệu.
4. Tạo thư mục lưu dữ liệu trên laptop, ví dụ: D:\Tablet_Backup.

## 2. Sao chép dữ liệu từ Tablet 1 về laptop
**Bước 1**: Kết nối Tablet 1 với laptop
- Cắm cáp USB kết nối Tablet 1 với laptop.
- Trên tablet, chọn **Allow USB Debugging** nếu được hỏi.
- Mở thư mục `platform-tools`, nhập `cmd` trên thanh địa chỉ và nhấn Enter.

**Bước 2**: Kiểm tra kết nối ADB
```
.\adb devices
```

Kết quả thành công:
```
List of devices attached
R52XXXXXXXX    device
```

**Bước 3**: Kiểm tra dữ liệu trong Download
```
.\adb shell ls -la /sdcard/Download/
```

**Bước 4**: Tạo thư mục sao lưu trên laptop
```
mkdir D:\Tablet_Backup
```

**Bước 5**: Sao chép toàn bộ dữ liệu về laptop
```
.\adb pull /sdcard/Download/. "D:\Tablet_Backup"
```

Sau khi hoàn tất, kiểm tra các file trong `D:\Tablet_Backup`.

## 3. Chuyển dữ liệu từ laptop sang Tablet 2
**Bước 1**: Kết nối Tablet 2
- Ngắt kết nối Tablet 1.
- Cắm Tablet 2 vào laptop.
- Bật USB Debugging và chọn Allow khi được yêu cầu.

**Bước 2**: Kiểm tra kết nối
```
.\adb devices
```

Đảm bảo thiết bị hiển thị trạng thái `device`.

**Bước 3**: Tạo thư mục Download nếu chưa có
```
.\adb shell mkdir -p /sdcard/Download
```

## Bước 4: Chuyển toàn bộ dữ liệu sang Tablet 2
```
.\adb push "D:\Tablet_Backup\." /sdcard/Download/
```

Lệnh trên sao chép toàn bộ file và thư mục con từ laptop vào Download trên Tablet 2.

## Bước 5: Kiểm tra kết quả
```
.\adb shell ls -la /sdcard/Download/
```

Hoặc trên Tablet 2, truy cập:

`My Files → Bộ nhớ trong → Download`

## 4. Một số lệnh ADB thường dùng
| Lệnh |	Chức năng |
| --- | --- |
| `.\adb devices`	| Kiểm tra thiết bị kết nối |
| `.\adb shell ls /sdcard/Download/`	| Xem danh sách file |
| `.\adb pull /sdcard/Download/. "D:\Tablet_Backup"`	| Sao chép dữ liệu về laptop |
| `.\adb push "D:\Tablet_Backup\." /sdcard/Download/`	| Chuyển dữ liệu sang tablet |
| `.\adb install "D:\Tablet_Backup\app.apk"`	| Cài đặt ứng dụng APK |
| `.\adb shell mkdir -p /sdcard/Download`	| Tạo thư mục Download |

## 5. Lưu ý
- `adb pull`: Sao chép dữ liệu từ tablet về laptop.
- `adb push`: Sao chép dữ liệu từ laptop sang tablet.
- Hai lệnh trên không xóa dữ liệu gốc.
- Nếu Tablet 2 đã có file trùng tên và đường dẫn, dữ liệu có thể bị ghi đè. Nên sao lưu trước khi thực hiện.
- Chỉ sao chép được những file mà ADB có quyền truy cập.
- Sao chép APK không đồng nghĩa với sao chép dữ liệu ứng dụng, cấu hình VPN, chứng chỉ bảo mật hoặc thông tin đăng nhập.
VI. Quy trình tổng quát

<img width="767" height="414" alt="image" src="https://github.com/user-attachments/assets/c099cef9-2d78-447a-a15c-876df7e89002" />

**Kết quả**: Toàn bộ file và thư mục con trong Download của Tablet 1 được sao chép sang Download của Tablet 2 thông qua laptop bằng công cụ ADB.
