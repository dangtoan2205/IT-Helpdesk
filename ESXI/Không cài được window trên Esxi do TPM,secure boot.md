Hướng dẫn sửa lỗi "This pc doesn't meet the minimum system requirements to install this version of Windows. For more information, visit http://aks.ms/WindowsSysReq". Không cài được Windows 11 trên ESXI.
-----------

✅ Yêu cầu tối thiểu của Windows 11:
TPM 2.0 (Trusted Platform Module)

Secure Boot

CPU hỗ trợ (chẳng hạn Intel Gen 8 trở lên)

RAM tối thiểu 4GB

64GB ổ cứng

2 nhân CPU trở lên

✅ Cách khắc phục lỗi khi cài Win 11 trên ESXi

Cách 1: Dùng bản ISO đã bypass yêu cầu TPM/Secure Boot.

Có nhiều bản Windows 11 ISO đã được "mod" để bỏ qua yêu cầu TPM và Secure Boot. Bạn có thể dùng những bản này để cài dễ dàng hơn (ví dụ: Tiny11, GhostSpectre,...).

Nhược điểm: không chính thức, không phù hợp với môi trường cần tính bảo mật cao.

Cách 2: Dùng registry hack trong lúc cài Windows

Khi đang ở màn hình báo lỗi, bạn nhấn:

1. Shift + F10 để mở Command Prompt

2. Gõ lệnh:

```
regedit
```

3. Truy cập khóa:

```
HKEY_LOCAL_MACHINE\SYSTEM\Setup
```

4. Tạo key con tên:

```
LabConfig
```

5. Trong LabConfig, tạo 3 giá trị DWORD (32-bit):

BypassTPMCheck = 1

BypassSecureBootCheck = 1

BypassCPUCheck = 1 (tùy chọn)

6. Đóng Registry Editor, gõ lệnh:

```
exit
```

7.  Quay lại giao diện cài đặt và nhấn Next để tiếp tục.


Cách 3: Cấu hình máy ảo trong ESXi để đáp ứng yêu cầu

✔ Đảm bảo cấu hình máy ảo:

VM hardware version 14+

Enable Secure Boot

Enable TPM (virtual TPM):

Edit VM settings → Add New Device → Trusted Platform Module (TPM)

Bật "UEFI" và "Secure Boot"

CPU: 2 vCPU trở lên

RAM: tối thiểu 4GB

Lưu ý: TPM chỉ có trên ESXi 7 trở lên và yêu cầu vCenter nếu dùng TPM 2.0 ảo hóa.


✅ Gợi ý: Dùng công cụ tạo ISO bypass TPM
Bạn có thể dùng script như MediaCreationTool.bat để tạo bản ISO đã bypass sẵn TPM, CPU check, v.v.

```
https://github.com/AveYo/MediaCreationTool.bat
```















