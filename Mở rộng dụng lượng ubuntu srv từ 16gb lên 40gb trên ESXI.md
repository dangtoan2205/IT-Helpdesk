## 1. Thêm dung lượng trên ESXI

1/ Truy cập ESXI

2/ Tắt máy ảo ubuntu server đang chạy

3/ Vào Edit settings

4/ Thêm dung lượng cho máy ảo

![image](https://github.com/user-attachments/assets/2c4dfb84-6baf-491f-a131-56e09566c4a9)

-> Save

5/ Khởi động lại ubuntu server

## 2. Mở rộng dung lượng bộ nhớ trên Ubuntu Server

Kiểm tra bằng câu lệnh

![image](https://github.com/user-attachments/assets/9a39b5cd-fa0d-4659-93a6-837a13b82274)

```
lsblk
```

![image](https://github.com/user-attachments/assets/9a89f8d5-e969-4030-9f7e-91cf08b1aabd)

```
df -h
```

-> Dựa vào thư mục trên ta thấy như sau: 

![image](https://github.com/user-attachments/assets/97f865a8-af66-4573-aed1-2cfbf0d51930)

Dung lượng tổng là 40G 
  - sda 40G

Dung lượng hiện tại sử dụng là 16G
  - sda2 16G

-> Kết quả của lệnh lsblk cho thấy ổ đĩa chính /dev/sda đã tăng lên 40GB, nhưng phân vùng /dev/sda2 vẫn giữ nguyên 16GB. Để gộp phần không gian còn lại vào phân vùng /dev/sda2, bạn làm theo hướng dẫn sau:
------------

1/ Xác nhận rằng phân vùng `/dev/sda2` sử dụng hệ thống tệp `ext4` bằng lệnh:

```
sudo file -s /dev/sda2
```

Nếu kết quả trả về là `ext4`, bạn có thể tiếp tục.

![image](https://github.com/user-attachments/assets/21362420-e33a-4db8-9d13-7de3a8d7fb9a)

2/ Sử dụng `growpart` để mở rộng phân vùng

- Cài đặt công cụ:

```
sudo apt update
sudo apt install cloud-guest-utils
```

- Mở rộng phân vùng /dev/sda2 để chiếm toàn bộ dung lượng còn lại:

```
sudo growpart /dev/sda 2
```

![image](https://github.com/user-attachments/assets/8f2537ee-04f7-4456-9b2c-bbfffbc08a66)

- Sau khi mở rộng phân vùng, cập nhật hệ thống tệp:

```
sudo resize2fs /dev/sda2
```

![image](https://github.com/user-attachments/assets/82abf17c-e36d-4cec-9b53-e2eaa4a0410c)

Việc mở rộng hoàn tất

![image](https://github.com/user-attachments/assets/5ce88134-a624-436a-b075-fe3ac6614450)

![image](https://github.com/user-attachments/assets/a860bcc3-ac08-4e2d-812e-6483aba613ef)



------------------
Nếu không dùng growpart, thực hiện thủ công với fdisk
----------------------------

Nếu `growpart` không khả dụng, bạn có thể chỉnh sửa phân vùng bằng `fdisk`.

1/ Sử dụng `fdisk` để xóa và tạo lại phân vùng

- Chạy `fdisk`:

```
sudo fdisk /dev/sda
```

- Hiển thị danh sách phân vùng:

Nhấn `p`

- Xóa phân vùng `/dev/sda2` (không làm mất dữ liệu nếu thực hiện đúng):

Nhấn `d`, sau đó nhập `2` (hoặc số phân vùng tương ứng).

- Tạo lại phân vùng với kích thước lớn hơn:

Nhấn `n`, chọn `primary partition`, và nhập `2`.

`Quan trọng`: Đặt sector bắt đầu giống như sector bắt đầu của phân vùng cũ (thường tự động được giữ nguyên).

Nhấn `Enter` để sử dụng toàn bộ không gian còn lại.

- Lưu thay đổi:

Nhấn `w`

2/ Mở rộng hệ thống tệp

```
sudo resize2fs /dev/sda2
```

3/ Kiểm tra kết quả

```
df -h
```

```
lsblk
```







