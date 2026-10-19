TÀI LIỆU HƯỚNG DẪN SAO CHÉP ỨNG DỤNG (APP.APK) GIỮA HAI TABLET ANDROID BẰNG ADB
-----

## 1. Chuẩn bị:
**Bước 1**: Tải ADB chính thức từ Google:
[Link download](https://developer.android.com/tools/releases/platform-tools?utm_source=chatgpt.com&hl=vi)

**Bước 2**: Giải nén vào thư mục:
```
C:\platform-tools
```

**Bước 3**: Mở `PowerShell` hoặc `CMD` trong thư mục `platform-tools`.

## 2. Trích xuất APK từ Galaxy Tab S10 FE

**Bước 1**: Bật USB Debugging trên tablet nguồn

Vào:
```
Cài đặt → Thông tin máy tính bảng → Thông tin phần mềm
```
Nhấn 7 lần vào **Số hiệu bản tạo (Build number)** để bật Tùy chọn nhà phát triển.

Sau đó vào:
```
Cài đặt → Tùy chọn nhà phát triển → Gỡ lỗi USB (USB debugging) → Bật
```
**Bước 2**: Kết nối tablet với laptop bằng cáp USB

Trên laptop chạy:

```
.\adb devices
```

Trên Tablet sẽ hỏi cho phép gỡ lỗi USB, chọn **Allow**.

Kết quả mong đợi:
```
List of devices attached
R52XXXXXXX    device
```

**Bước 3**: Xác định package name của ứng dụng

Ví dụ ứng dụng có tên VPNSE:
```
.\adb shell pm list packages | findstr /i "vpn"
```

Kết quả ví dụ:
```
package:vn.example.vpnse
```

Đây chỉ là package minh họa, ta cần lấy tên thực tế.

**Bước 4**: Xác định đường dẫn APK
```
.\adb shell pm path vn.example.vpnse
```

Ví dụ kết quả:
```
package:/data/app/~~xxxx/base.apk
```

**Bước 5**: Copy APK về laptop
```
.\adb pull "/data/app/~~xxxx/base.apk" "C:\platform-tools\app.apk"
```

Thay đường dẫn /data/app/~~xxxx/base.apk bằng đường dẫn thực tế ở **bước 4**.

Sau khi hoàn tất, bạn sẽ có file:
```
C:\platform-tools\app.apk
```

> **Lưu ý quan trọng**: Nếu lệnh `pm path` trả về nhiều APK như `base.apk`, `split_config.arm64_v8a.apk`, `split_config.xxhdpi.apk` thì phải copy tất cả.
Chỉ lấy `base.apk` có thể khiến ứng dụng không cài được hoặc không hoạt động.

## 3. Cài ứng dụng từ laptop sang tablet thứ hai
**Bước 1**: Rút tablet nguồn và kết nối tablet đích với laptop.

**Bước 2**: Bật USB Debugging trên tablet đích tương tự bước trên.

**Bước 3**: Kiểm tra kết nối:
```
.\adb devices
```

**Bước 4**: Cài ứng dụng:
```
.\adb install "C:\platform-tools\app.apk"
```

Nếu thành công:
```
Performing Streamed Install
Success
```


Nếu ứng dụng gồm nhiều split APK, sử dụng:
```
.\adb install-multiple base.apk split_config.arm64_v8a.apk split_config.xxhdpi.apk
```

> Điền đầy đủ các file APK đã trích xuất, đúng theo ứng dụng thực tế.

## 4. Ứng dụng sau khi copy có hoạt động bình thường không?
| Trường hợp	| Khả năng hoạt động |
| --- | --- |
| APK thông thường, không yêu cầu kích hoạt	| Thường hoạt động |
| APK gồm nhiều split APK	| Hoạt động nếu cài đủ các phần |
| Ứng dụng yêu cầu đăng nhập	| Cần đăng nhập lại |
| Ứng dụng VPN có chứng chỉ riêng	| Có thể cần cài chứng chỉ và cấu hình |
| Ứng dụng quản lý bằng SOTI MobiControl	| Có thể phụ thuộc chính sách MDM |
| Ứng dụng khóa theo Device ID/IMEI	| Có thể không hoạt động trên máy khác |

**Đặc biệt**: Việc trích xuất APK chỉ sao chép bộ cài ứng dụng, không sao chép dữ liệu, tài khoản đăng nhập, chứng chỉ riêng hoặc giấy phép kích hoạt.


















