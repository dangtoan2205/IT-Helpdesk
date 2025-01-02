Cách xây dựng lưu trữ mạng và máy chủ truy cập từ xa của riêng.
------------

<a href="https://www.youtube.com/watch?v=_Nu5Ibv1ZQA"> Link Youtube </a>

Chuẩn bị: 
  - Case PC : 1 ổ cứng SSD 12Gb, 1 ổ cứng HDD 1Tb
  - Phần mềm: Rufus, TrueNAS-SCALE-24.04.2.3.iso




## Step 1: Tải OS cho TrueNAS

Login with google link: <a href="https://www.truenas.com/download-truenas-scale/?submissionGuid=4791a5aa-816c-4fee-bb1d-6154f6a80a51"> Link download </a> -> Download STABLE

![image](https://github.com/user-attachments/assets/fc4abf55-9e0f-491a-8049-51d8c795003a)

## Step 2: Tạo usb boot bằng phần mềm Rufus

  - Partition scheme: MBR
  - File system: Large FAT32 (Default)

![image](https://github.com/user-attachments/assets/715c836f-afcd-4475-8ad5-a4b238f648e6)

## Step 3: Install OS TrueNAS

![image](https://github.com/user-attachments/assets/08b45934-873e-467f-9260-2e64a2030457)

![image](https://github.com/user-attachments/assets/6c946168-8b9d-4337-8b1b-35a9c898f3a3)

Chọn 1 -> OK

![image](https://github.com/user-attachments/assets/d4ec1167-b706-4676-bb48-474e31b3d9d7)

Chọn ổ cài (Bấm Space) -> OK

![image](https://github.com/user-attachments/assets/12a00c9b-fe34-4a6a-8847-cde8855ed729)

Chọn Yes

![image](https://github.com/user-attachments/assets/fe5af133-63b9-468d-9767-1c541ecf7b81)

Hiện ra yêu cầu tạo mật khẩu mới: chọn 1 -> OK (Đây là mật khẩu được sử dụng để truy cập bằng trình duyệt)

![image](https://github.com/user-attachments/assets/d10cff0a-36af-42bf-b96c-4f29434e8608)

Nhập mật khẩu -> Chọn OK

![image](https://github.com/user-attachments/assets/54c33274-559a-45eb-8344-42bf9a4f88aa)

Chọn Create swap 

![image](https://github.com/user-attachments/assets/c6d46e9e-4eb5-4a98-a1be-7198030bb852)

Chọn Yes

![image](https://github.com/user-attachments/assets/1beb069b-f6ff-48b1-b279-2641824e7ccb)

Chọn OK

![image](https://github.com/user-attachments/assets/68ca63e4-6088-4524-be12-83a2fadd8c7b)

Chọn lựa chọn 3 -> OK

## Step 4: Cấu hình trên giao diện trình duyệt

![image](https://github.com/user-attachments/assets/51d8a9ae-635c-43c6-8ac4-f1ecdf300528)

Đây là địa chỉ DHCP của TrueNAS : 192.168.1.16

![image](https://github.com/user-attachments/assets/342f2317-1c6b-4c51-a925-54175864948d)

Nhập tài khoản quản trị:
  - Username: admin
  - Password: <mk quản trị tạo ở bước 3>

## Step 5: Setup cơ bản

- Cài đặt múi giờ:
  - - - -

  System Settings -> General -> Localization -> Settings

  ![image](https://github.com/user-attachments/assets/940f30e7-4f01-4178-a430-4416f24180b2)

  Timezone: Asia/Ho_Chi_Minh -> Save

- Tạo một tài khoản mới, hạn chế dùng đến tài khoản quản trị
  - - - -

  Credentials -> Local User -> Add 

  ![image](https://github.com/user-attachments/assets/1ec7e7fe-fc29-47c0-87d6-17ea1b33478f)

  ![image](https://github.com/user-attachments/assets/313ad6eb-2248-4cd8-ab7c-ff28ab62f4a5)

  ![image](https://github.com/user-attachments/assets/ad549624-5e87-47b0-9f94-f8efaa4e7ad2)

  Bấm chọn Save

- Tạo ổ lưu trữ
  - - - - 

    Storage -> Storage Dashboard -> Create Pool

  ![image](https://github.com/user-attachments/assets/f0c36c1e-18b7-40c4-8d37-4a958bfca299)

  -> Next

  ![image](https://github.com/user-attachments/assets/1f9a9b38-4b13-433f-b71f-a9c4be1202f1)

  Data -> Layout: Mirror < Để tránh nguy cơ mất dữ liệu có thể sử dụng 2 ổ cứng HDD 1Tb và lựa chọn bộ lưu trữ phản chiếu 2 chiều -> Disk size 

  ![image](https://github.com/user-attachments/assets/ab063cae-4f46-46c9-aca0-0cbd06d2aa2a)

  -> Next

  ![image](https://github.com/user-attachments/assets/11d28925-13ed-4543-8637-cee68e6bf02a)

  -> Next

  ![image](https://github.com/user-attachments/assets/d8d99d98-e27d-4c05-af1b-b1dbba62f842)

  -> Next

  ![image](https://github.com/user-attachments/assets/ee3c9aed-10c5-49f9-919a-6cdb71a1f31a)

  -> Next

  ![image](https://github.com/user-attachments/assets/eebee43e-79fe-4ba2-a391-f08950e894ce)

  -> Next

  ![image](https://github.com/user-attachments/assets/a65f62aa-666b-4cbe-b65e-a2c63d501609)

  -> Create Pool

  ![image](https://github.com/user-attachments/assets/9faa817b-3156-42fb-840f-84de5e1dfa4b)

  Tích Confirm -> Configure 

- Datasets -> Add Datasets
  - - - - -
 
  ![image](https://github.com/user-attachments/assets/dee5bd75-81cb-4700-9015-de02542128a9)

  ![image](https://github.com/user-attachments/assets/d42f0e80-17f1-4ee8-8ffa-ee5ff436db28)

  ![image](https://github.com/user-attachments/assets/08df16d4-7be0-4b42-9595-0223e5ac39fe)

- Shares -> Edit Filesystem ACL
  - - - - -

  ![image](https://github.com/user-attachments/assets/a72cf84a-d03e-4c97-aaca-69c796c0e3a8)

  ![image](https://github.com/user-attachments/assets/8c0a80a6-4673-4b5f-9efc-5a1270803948)

  Add Item 

  ![image](https://github.com/user-attachments/assets/8b092e1e-b471-44de-850e-3826a699c4fe)

  Chọn Modifi

  ![image](https://github.com/user-attachments/assets/152b4588-25b3-4bbe-819b-2eb4970bc94c)

  Chọn Save Access Control List

- Thiết lập tài khoản có quyền đọc và ghi đối tượng với thư mục được chia sẻ qua internet
  - - - - -

  ![image](https://github.com/user-attachments/assets/ff4861fd-232b-4e20-8fc2-d11560e706ff)
 
  Chọn Network

  ![image](https://github.com/user-attachments/assets/959b804d-f345-40b6-8acd-e678359e3d7d)

  Edit

  ![image](https://github.com/user-attachments/assets/ea9afd24-72ff-4d2c-ab9f-5ea38991153a)
  
  Thiết lập IP tĩnh cho máy chủ lưu trữ mạng. Đặt IP tĩnh để cấu hình chuyển tiếp cổng

  ![image](https://github.com/user-attachments/assets/7da56992-aa2e-417c-bcce-fd6f52823000)

  Skip

  ![image](https://github.com/user-attachments/assets/1d4c6df0-2859-45dc-b669-acdedb461fae)

  Test Changes

  ![image](https://github.com/user-attachments/assets/40c5fe79-2dd3-4983-a96f-94beedc151de)

  Tích chọn Confirm -> Test Chages

  ![image](https://github.com/user-attachments/assets/d2759f63-b606-42b2-b26d-33ebaa24830b)

  Save Changes -> Save

  ![image](https://github.com/user-attachments/assets/8ad9e486-4467-4b62-b82a-529282e1c408)

  Settings

  ![image](https://github.com/user-attachments/assets/6ad21ff4-0fa4-4261-b33d-e75f7fa159c2)

  ![image](https://github.com/user-attachments/assets/3408c047-1583-41a5-8b62-495708e9ec5d)

  Save

  ![image](https://github.com/user-attachments/assets/f4034bef-6277-4cf1-8045-fbb8633fa350)  

## Step 6: truy cập File serve bằng mạng cục bộ

- Window + R : \\192.168.1.16

- Đăng nhập tài khoản, mật khẩu

![image](https://github.com/user-attachments/assets/2881e422-0c6a-4c08-af8d-fac0374a294a)

## Step 7 : Cấu hình mạng riêng ảo

- Vào App -> tìm kiếm WG Easy
  - - - - -

  ![image](https://github.com/user-attachments/assets/b2bf0cca-b4b1-4c4b-8320-3ff066823227)

  ![image](https://github.com/user-attachments/assets/7ffc9953-2893-4c4c-9847-9d19ed7966e3)

  Setup Pool To Install

  ![image](https://github.com/user-attachments/assets/9d598c1f-f9cd-4e65-a71c-58baed5eff6b)

  Choose a pool for App -> pool1 -> Choose

  ![image](https://github.com/user-attachments/assets/e70a6159-5c52-4927-99f0-385bd3176ebe)

  Tích Confirm -> Agree

  ![image](https://github.com/user-attachments/assets/d7390900-15fb-40fc-8993-2193b075d83c)

  ![image](https://github.com/user-attachments/assets/bbf37625-7a79-47ce-85a2-7c172241d9f3)

  Hostname or IP sẽ sử dụng IP Public của nhà mạng cung cấp

  ![image](https://github.com/user-attachments/assets/3e6831d1-eb31-4970-aeeb-444f578e6a16)

  Password for WebUI -> Tự tạo

  Device Name: enp5s0 mà ta đã sao chép từ trước

  ![image](https://github.com/user-attachments/assets/fd9008fd-4982-4f1b-8aff-34c1a528156a)

  ![image](https://github.com/user-attachments/assets/14ac6512-7213-4b55-82c1-b5186729a449)

  Memory giảm xuống còn 2Gi

  ![image](https://github.com/user-attachments/assets/7df042fc-5836-4f34-83cb-315d9838f325)

  ![image](https://github.com/user-attachments/assets/20c9d4ee-aadb-4600-8269-9fb2121bfde4)

  Bấm vào Web Portal để truy cập máy chủ mạng riêng ảo

  ![image](https://github.com/user-attachments/assets/977f75b9-f355-414e-87d1-7be89b90a5ca)

- Tiếp theo là tạo tệp cấu hình mới cho máy khách
  - - - - -

  ![image](https://github.com/user-attachments/assets/ba259e7b-fb0c-442e-ba24-fbf1c7f91288)

  New Client

  ![image](https://github.com/user-attachments/assets/877713a8-0155-4c1b-a704-a8125c269443)

  Tạo Client mới -> User01 -> Create

  ![image](https://github.com/user-attachments/assets/5aa07910-11e8-41a7-8234-35c56ca584e4)

  Tải xuống tệp cấu hình

  ![image](https://github.com/user-attachments/assets/767a5ba9-380e-465d-a417-aeabdc771542)

  Chỉ định địa chỉ IP công khai cho máy chủ lưu trữ tệp. Vào trang quản lý bộ định tuyến 

  ![image](https://github.com/user-attachments/assets/33f5a325-0bac-40bb-bacd-14d193ab3ded)


  Để sử dụng được thì ta tải Wireguard Client về máy tính -> Import tunnel bằng User01.còn vừa được tạo
  ----

  ![image](https://github.com/user-attachments/assets/9ede0da7-99a8-4a74-a27f-4d5700453e5d)

  ![image](https://github.com/user-attachments/assets/85941e43-264c-47d9-b9ec-53df11733856)

  Bấm Active để kiểm tra
  
  ## Step 8: Sử dụng tên miền tự động để khắc phục nhược điểm của địa chỉ IP công khai động

![image](https://github.com/user-attachments/assets/27f8f025-5fb9-482f-b18b-2b6ceea36ca3)














