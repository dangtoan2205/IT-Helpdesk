Cách xây dựng lưu trữ mạng và máy chủ truy cập từ xa của riêng.
------------


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

  System Settings -> General -> Localization -> Settings

  ![image](https://github.com/user-attachments/assets/940f30e7-4f01-4178-a430-4416f24180b2)

  Timezone: Asia/Ho_Chi_Minh -> Save

- Tạo một tài khoản mới, hạn chế dùng đến tài khoản quản trị

  Credentials -> Local User -> Add 

  ![image](https://github.com/user-attachments/assets/1ec7e7fe-fc29-47c0-87d6-17ea1b33478f)

  ![image](https://github.com/user-attachments/assets/313ad6eb-2248-4cd8-ab7c-ff28ab62f4a5)

  ![image](https://github.com/user-attachments/assets/ad549624-5e87-47b0-9f94-f8efaa4e7ad2)

  Bấm chọn Save

  - Tạo ổ lưu trữ

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
















































