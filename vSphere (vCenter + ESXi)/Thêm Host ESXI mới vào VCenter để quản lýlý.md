Hướng dẫn thêm Host ESXI vào VCenter
-----

## 1. Thành phần chức năng
| Thành phần         | Vai trò                                    | Quản lý cái gì?                                                   |
| ------------------ | ------------------------------------------ | ----------------------------------------------------------------- |
| **ESXi**           | Hypervisor cài trực tiếp lên server vật lý | VM, CPU, RAM, NIC, datastore của chính server đó                  |
| **vCenter Server** | Hệ thống quản lý tập trung                 | Nhiều ESXi Host, Cluster, VM, vMotion, HA, DRS, permissions...    |

## 2. Thêm Host ESXI mới vào VCenter
Trên `VCenter` nhấn chuột phải vào `Host` cần tạo

```
Add Host...
```

## 3. Điền các thông tin sau:
### 3.1. Name and location
- Host name or IP address: (Nhập thông tin IP của server ESXI)
- Location: (Là địa chỉ lưu thông tin vào Host nào trên VCenter)

-> Nhấn Next

### 3.2. Connection settings 
- User name: (Tài khoản đăng nhập của ESXI)
- Password: (Mật khẩu đăng nhập của ESXI)

-> Nhấn Next

Chọn `Yes` để xác nhận

### 3.3. Host summary
Kiểm tra thông tin ESXI cần thêm vào VCenter
- Name: IP server ESXi
- Vendor: Dell Inc
- Model: PowerEdge R440
- Version: VMware ESXI 8.0.3 build-25205845
- Virtual Machines: (Các thông tin VM đang có trong ESXI này)

-> Nhấn Next

### 3.4. Host lifecycle
- Bỏ tích chọn: `Manage host with an image`

-> Nhấn Next

### 3.5. Assign license
Nếu không có license thì chọn `Evaluation License` -> Sẽ hết hạn sau 19 n

-> Nhấn Next

### 3.6. Lockdown mode 
Nếu không sử dụng thì chọn `Disable`

-> Nhấn Next

### 3.7. VM location
Nơi lưu trữ host ESXI này

-> Nhấn Next

### 3.8. Ready to complete 
Review lại các thôn tin của ESXI

-> Nhấn Finish để hoàn thành.













