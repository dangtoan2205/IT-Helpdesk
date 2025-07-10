
## Bước 1: Tải và cài đặt phần mềm Scan IP

[Link download SADP](https://www.hikvision.com/vn/support/tools/hitools/clea8b3e4ea7da90a9/)

## Bước 2: Cài đặt IP Camera

Ta sẽ thấy hình ảnh xuất hiện sẽ xuất hiện `Status` -> `Active` hoặc `Inactive`

Chọn thiết bị `Inactive` và nhập mật khẩu cho mắt camera

![image](https://github.com/user-attachments/assets/ffdab06a-dfee-43b5-857c-12de8220a772)

-> Dùng để xác thực cũng như kích hoạt camera

## Bước 3: Đặt địa chỉ IP cho mắt camera

![image](https://github.com/user-attachments/assets/0f4f311f-90a8-4a32-97ad-85f28a0f889d)

- IP phải cùng 1 dải, cùng với đầu ghi.
- IP các mắt camera không được trùng nhau.

Tiến hành nhập IP mắt camera và `Administrator Password`

![sleekshot](https://github.com/user-attachments/assets/f06ee3d5-e76b-42f3-a870-ef738db1769b)

## Bước 4: Cài đặt đầu ghi

Cắm nguồn đầu ghi và cắm dây mạng vào đầu ghi. Sau khi thực hiện xong thì vào phần mềm SADP để quét lấy địa chỉ IP của đầu ghi.

![image](https://github.com/user-attachments/assets/7b846749-182a-4347-b8f8-16c6bcd0a355)

Trong trường hợp này IP mặc định của đầu ghi là `192.168.1.168`

Nhập mật khẩu mới cho đầu ghi (Mật khẩu này sẽ dùng để đăng nhập cũng như dùng để xác thực sau này).

![image](https://github.com/user-attachments/assets/2162c4e6-9115-40f5-8af5-b2177d28f449)

Nhập 1 mã code để xác thực với đầu ghi Hikconnect

![image](https://github.com/user-attachments/assets/42351032-f4b3-491a-bf00-8608092499e7)

Đầu ghi đã được kích hoạt thành công:

![image](https://github.com/user-attachments/assets/9a421975-0d01-4d50-a2a0-c19185cc440e)

## Bước 5: Truy cập đầu ghi bằng IP và đặt IP tĩnh cho đầu ghi

Set IP của laptop thành cùng dải IP với IP mặc định của đầu ghi `192.168.1.168`.

![image](https://github.com/user-attachments/assets/0cc527fe-df70-441a-bd08-ad5d7e7e0133)

Tiến hành truy cập IP này với trình duyệt

```bash
192.168.1.168
```

Nhập tài khoản và mật khẩu nãy vừa tạo xong.

![image](https://github.com/user-attachments/assets/906132bc-f828-4fd3-b9c4-03e4bf3981fe)

Tiến hành đặt IP tĩnh cho đầu ghi này

![sleekshot](https://github.com/user-attachments/assets/29d4aa3c-8559-44e2-8bf0-34b24d8c6fcc)

## Bước 6: Định dạng ổ đĩa lưu trữ

Tiến hành đặt lại đầu ghi

![sleekshot](https://github.com/user-attachments/assets/b960d945-4fdb-4af8-a488-2840bf9cb383)

Bấm chọn `Đồng ý`

![image](https://github.com/user-attachments/assets/2a48ce87-58ca-4c38-abfa-442aa9ff9336)

## Bước 7: Thêm mắt vào đầu ghi

![sleekshot](https://github.com/user-attachments/assets/f0f099b7-bd2e-42ba-8189-d5c60ab6c834)

Thêm mắt camera vào giao diện quản lý camera trên đầu ghi

![sleekshot](https://github.com/user-attachments/assets/9697d493-3a6a-4d18-9b5d-cf9a448d422a)

Camera lúc này vẫn chưa hoạt động được cần thực hiện các bước sau để active camera

Tích chọn `Camera` và chọn `Chỉnh sửa`

![sleekshot](https://github.com/user-attachments/assets/4f40a9a4-e03b-432e-9e1e-d346f5a61e36)

Lưu ý: Ta cần nhập mật khẩu của mắt camera chứ không phải mật khẩu của đầu ghi.

![sleekshot](https://github.com/user-attachments/assets/5f1053fa-d7cf-4681-90eb-7e1f7d2c674c)


![sleekshot](https://github.com/user-attachments/assets/61831870-65d5-42b4-963d-9e2d809ae145)

## Bước 8: Thêm đầu ghi camera vào app Hik-Connect trên điện thoại

Quét mã QR code bên dưới đầu ghi

