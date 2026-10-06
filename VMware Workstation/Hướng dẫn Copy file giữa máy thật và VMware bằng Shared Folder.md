Hướng dẫn Copy file giữa máy thật và VMware bằng Shared Folder
---

## 1. Copy file từ trong VM ra máy thật

**Bước 1: Tạo Shared Folder trên VMware**

Tắt VM hoặc để VM chạy, sau đó vào:

**VM → Settings → Options → Shared Folders**

Chọn:

**Always enabled → Add...**

Tại **Host path**, chọn thư mục trên máy thật, ví dụ:

```
C:\VM-Share
```
Đặt tên:

```
VMShare
```

**→ Finish → OK**

**Bước 2: Copy file từ VM vào Shared Folder**

Trong VM mở **File Explorer**, nhập:

```
\\vmware-host\Shared Folders\VMShare
```

Ví dụ file cần lấy ra:
```
C:\Backup\backup.zip
```

Copy:
```
C:\Backup\backup.zip
```

→ Paste vào:
```
\\vmware-host\Shared Folders\VMShare
```

**Bước 3: Lấy file trên máy thật**

Trên máy thật mở:
```
C:\VM-Share
```

Bạn sẽ thấy:
```
backup.zip
```

→ Copy file này ra vị trí mong muốn trên máy thật.

## 2. Copy file từ máy thật vào VM

**Bước 1: Copy file vào Shared Folder**

Trên máy thật, ví dụ có:
```
C:\Users\Toan\Desktop\setup.exe
```

Copy:
```
setup.exe
```

vào:
```
C:\VM-Share
```

** Bước 2: Vào Shared Folder trong VM**

Trong VM mở File Explorer, nhập:
```
\\vmware-host\Shared Folders\VMShare
```

Bạn sẽ thấy:
```
setup.exe
```

**Bước 3: Copy file vào VM**
Copy:
```
setup.exe
```

vào thư mục trong VM, ví dụ:
```
C:\Install
```
Kết quả:
```
C:\Install\setup.exe
```
-------------
**Tóm tắt**

```
VM → Máy thật

VM
 ↓
\\vmware-host\Shared Folders\VMShare
 ↓
C:\VM-Share
 ↓
Máy thật
```

```
Máy thật → VM

Máy thật
 ↓
C:\VM-Share
 ↓
\\vmware-host\Shared Folders\VMShare
 ↓
VM
```

**Điều kiện**: VM phải được cài **VMware Tools** và **Shared Folder** phải được bật **Always enabled**.
