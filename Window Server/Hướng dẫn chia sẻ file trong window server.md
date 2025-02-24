Hướng dẫn chia sẻ file trong window server
--------------

## Bước 1: Thêm user

- Tìm đến **Computer Management (Local)** -> **Local Users and Groups** -> **Users**
- **New User**
  - Tích chọn: **User cannot change password**
  - Tích chọn: **Password never expires**

![image](https://github.com/user-attachments/assets/7f45f477-7267-46ba-9a4c-927a5c7dc3a8)

## Bước 2: Tạo thư mục chia sẻ

- **This PC** -> **Local Disk (C:)** -> **New** -> **Folder**


## Bước 3: Thiết lập Share Folder

- Bấm chuột phải vào folder vừa tạo -> **Properties** -> Tab **Sharing**

![image](https://github.com/user-attachments/assets/05f9f7cf-051c-45c4-aead-794c29c0b145)

-> **Share**

![image](https://github.com/user-attachments/assets/4ef6bd94-a2fe-4229-863c-5bf19e5ef338)


- Nếu chọn **Everyone** thì sẽ chia sẻ cho tất cả user

-> Chọn **User cần share file** -> **Add** -> **Share** -> **Done** -> **Close**


- Vào window 11
- Truy cập vào ip của server

```
\\192.168.80.102
```

- Nhập username, password cho user được share file


Thêm quyền cho user mới
----------------

1/ Vào thư mục cần share -> Chuột phải chọn **Properties** -> 

2/ Chọn tab **Security** 

-> Chọn **Edit** để xóa user không được quyền truy cập folder

-> Chọn **Advanced** để thêm user và quyền cho user đó tác động như thế nào lên folder

![image](https://github.com/user-attachments/assets/28698e91-25e8-4678-a37d-9c7b93385371)


- Chọn **Add** -> Chọn **Select a principal**

![image](https://github.com/user-attachments/assets/28853068-4923-4e0b-9674-de6b3c67f51d)

- Chọn quyền tác động

![image](https://github.com/user-attachments/assets/21f800d5-0abb-43a7-a4d8-686a0e5fbea4)




















