Cấp quyền App Registration truy cập duy nhất 1 SharePoint Site (Sites.Selected)
----

# 🎯 Mục tiêu
- App chỉ truy cập được 1 site cụ thể
- Không có quyền toàn bộ SharePoint
- Đảm bảo least privilege (bảo mật chuẩn Microsoft)

# 📌 Thông tin áp dụng
Thành phần	Giá trị
App Name	PMO
Client ID	f9a5f6e6-0e3a-411b-8738-6df0057c0f13
Site URL	https://setainternationalvn.sharepoint.com/sites/PMO
Permission	write

# ⚙️ PHẦN 1 — CẤU HÌNH APP REGISTRATION
---
## Bước 1: Vào App Registration

👉 Microsoft Entra ID

→ App registrations → chọn app PMO

## Bước 2: Cấp quyền API

Vào:

**👉 API permissions**

Thêm:
- API: Microsoft Graph
- Type: **Application permissions**
- Chọn:
```
Sites.Selected
```

## Bước 3: Admin consent

👉 Nhấn:
```
Grant admin consent
```

# ⚙️ PHẦN 2 — CẤP QUYỀN CHO SITE (QUAN TRỌNG NHẤT)
---
⚠️ Nếu không làm bước này → App sẽ KHÔNG truy cập được site

# 🖥️ PHẦN 3 — THỰC HIỆN TRÊN POWERSHELL

Bước 1: Cài Microsoft Graph module
```
Install-Module Microsoft.Graph -Scope CurrentUser -Force
```

Bước 2: Đăng nhập
```
Connect-MgGraph -Scopes "Sites.FullControl.All"
```
👉 Login bằng tài khoản admin
```
Bước 3: Lấy Site ID
$site = Get-MgSite -SiteId "setainternationalvn.sharepoint.com:/sites/PMO"
$site.Id
```
👉 Kết quả:
```
setainternationalvn.sharepoint.com,5c894846-a76b-42cb-ac9a-79f8f4a49ef5,22175baa-8c73-4633-b9bb-a32f347c4b7e
```

Bước 4: Gán quyền cho App
```
$siteId   = "setainternationalvn.sharepoint.com,5c894846-a76b-42cb-ac9a-79f8f4a49ef5,22175baa-8c73-4633-b9bb-a32f347c4b7e"
$clientId = "f9a5f6e6-0e3a-411b-8738-6df0057c0f13"
$appName  = "PMO"

$params = @{
    roles = @("write")
    grantedTo = @{
        application = @{
            id = $clientId
            displayName = $appName
        }
    }
}

New-MgSitePermission -SiteId $siteId -BodyParameter $params
```

Bước 5: Kiểm tra kết quả
```
Get-MgSitePermission -SiteId $siteId | Format-List
```
👉 Kết quả mong đợi:

Roles : {write}

# ✅ PHẦN 4 — KẾT QUẢ

Sau khi hoàn thành:

✔ App chỉ truy cập được:
```
/sites/PMO
```
❌ Không truy cập được:

- Site khác
- Toàn tenant SharePoint
  
# 🔐 PHẦN 5 — NGUYÊN TẮC BẢO MẬT
- Sử dụng Sites.Selected thay vì:
  - ❌ Sites.Read.All
  - ❌ Sites.FullControl.All
- Chỉ cấp quyền khi cần
- Có thể revoke bất kỳ lúc nào

# 🔄 PHẦN 6 — THU HỒI QUYỀN

## Bước 1: Lấy Permission ID
```
Get-MgSitePermission -SiteId $siteId
```

## Bước 2: Xóa quyền
```
Remove-MgSitePermission -SiteId $siteId -PermissionId "<PERMISSION_ID>"
```

# ⚠️ PHẦN 7 — CÁC LỖI THƯỜNG GẶP
Lỗi	Nguyên nhân
403 Access Denied	chưa grant permission site
Không truy cập được	chưa admin consent
Không chạy PowerShell	chưa cài module
Dùng sai URL	dùng link _layouts

# 💡 PHẦN 8 — BEST PRACTICE

👉 Quy trình chuẩn IT:

1. User tạo site
2. User gửi URL cho IT
3. IT cấp quyền bằng PowerShell
4. App chỉ truy cập đúng site

# 🎯 KẾT LUẬN

Giải pháp này đảm bảo:

- ✔ Bảo mật cao
- ✔ Kiểm soát chính xác
- ✔ Chuẩn Microsoft Graph
- ✔ Phù hợp môi trường doanh nghiệp
