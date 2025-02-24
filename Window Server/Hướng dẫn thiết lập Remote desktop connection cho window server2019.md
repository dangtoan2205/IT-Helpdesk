Hướng dẫn thiết lập Remote desktop connection cho window server2019
-----------------


## Bước 1: **** Bật tính năng Remote Desktop

- Vào **Settings** -> **System** -> **Remote Desktop** -> Tích chọn **Enable Remote Desktop**

![image](https://github.com/user-attachments/assets/1ee6175d-a90d-49e3-aa34-b1697becb390)

## Bước 2: Đổi tên Desktop

- Vào **Server Manager** -> **Local Server** -> Chọn vào dòng có **Computer name: Administrator**

![image](https://github.com/user-attachments/assets/a1b4bce2-4ae1-4918-b5f4-04f650e490bd)

- Đặt mô tả server

![image](https://github.com/user-attachments/assets/1fb7369e-0c68-4c32-a3ef-26f721cd6341)

- Bấm vào **Change...** để đổi tên

![image](https://github.com/user-attachments/assets/854ec992-4000-430a-a019-b777e71bbe7c)


- Đặt tên mới **Administrator** -> OK -> Apply

## Bước 3: Đặt mật khẩu

- Giảm yêu cầu chính sách mật khẩu:

**Window + R**

```
secpol.msc
```

![image](https://github.com/user-attachments/assets/f722b15d-3768-4b17-b601-6aa23185fa60)

**Security Settings** -> **Account Policies** -> **Password Policy**

![image](https://github.com/user-attachments/assets/e5c12b2d-c67e-4b19-8f58-f349384b7fee)

1/ **Password must meet complexity requirements**: **Disabled**

2/ **Minimum password length**: **Đặt thành 8 ký tự.**

3/ **Maximum password age**: **Đặt thành 0**


- Cập nhật chính sách:

Mở **Command Prompt** với quyền **Administrator** và chạy:

```
gpupdate /force
```

- Đặt mật khẩu:

```
net user Administrator YourNewPassword
```

## Bước 4: Remote từ Desktop bên ngoài vào Winserver

- **Remote Desktop Connection**

Nhập IP: 192.168.80.102
Nhập username: Administrator
Nhập password: 

-> Connect








