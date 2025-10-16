LAB CONFIG
<img width="1095" height="860" alt="image" src="https://github.com/user-attachments/assets/e9980fed-b766-4231-bf3e-eef93721d09c" />


----
LƯU Ý MUỐN SỬ DỤNG SSH HOẶC TELNET ĐỂ KẾT NỐI TỚI SW L3 NÀY THÌ PHẢI ĐẶT USERNAM, PASSWORD TRƯỚC

Tạo mật khẩu mới:

```bash
ena

conf t
enable secret <mật_khẩu_mới>
username admin privilege 15 secret <mật_khẩu_mới>
line vty 0 4
 login local
 transport input ssh
```
