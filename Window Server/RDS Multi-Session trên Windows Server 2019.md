How to config multi session Remote Desktop for Windows Server
----

## Bước 1: Tạo tài khoản và cho phép quyền truy cập Remote Desktop

- TNhấp chuột phải vào nút **Start** sau đó chọn **Computer Management**

<img width="1011" height="884" alt="image" src="https://github.com/user-attachments/assets/41a0244f-b5be-4589-b910-108cfde32550" />

- Sau đó, chọn **Local Users and Group**, nhấp chuột phải vào **User** rồi chọn **New User**

<img width="996" height="716" alt="image" src="https://github.com/user-attachments/assets/f090aaf2-acc9-4424-a992-65dc9455e775" />

- Tạo tài khoản đăng nhập với đầy đủ thông tin như **User name**, **Password** rồi tích vào các lựa chọn cho phép `User đổi password` hoặc `password này có thời gian hết hạn hay không`. Nhấn **Create**

<img width="1105" height="756" alt="image" src="https://github.com/user-attachments/assets/35502b52-8a68-4a6a-87b9-42f54929ab7b" />

- Tiếp theo, cần cấu hình để cho phép `User` vừa tạo được phép kết nối vào `Windows Server qua Remote Desktop`. Nhấn **Windows + I** để mở **Settings** sau đó chọn **System**

<img width="1247" height="841" alt="image" src="https://github.com/user-attachments/assets/259f623a-9984-4e87-ad22-141f6efdb848" />

- Nhấn vào **About** ở thanh bên trái rồi kéo xuống ở thanh bên phải để chọn **Advanced system setting**. 

<img width="1051" height="825" alt="image" src="https://github.com/user-attachments/assets/e1d95618-a413-47ce-82d9-4fb374cee1d1" />

- Nếu không thấy **Advanced system setting** thì dùng **Control Panel** thì truy cập **System** -> **Advanced system setting**

<img width="1128" height="594" alt="image" src="https://github.com/user-attachments/assets/5a6bac6d-24b7-4b23-837d-6ba5b251cc44" />

- Trong cửa sổ mới hiện ra, nhấn thẻ **Remote** rồi nhấn nút **Select Users…**

<img width="415" height="481" alt="image" src="https://github.com/user-attachments/assets/f0f670a1-9494-4c70-8416-8c84412181cf" />

- Nhấn nút **Add** để thêm **User** được phép **Remote Desktop** vào.

<img width="403" height="431" alt="image" src="https://github.com/user-attachments/assets/8f54432d-cae2-406b-a420-d097c610223d" />

- Gõ đúng tên **User** vừa tạo sau đó nhấn **Check Names** hệ thống sẽ tự tìm ra tài khoản với đường dẫn chuẩn như hình. Nhấn **OK** để xác nhận chọn tài khoản

<img width="500" height="369" alt="image" src="https://github.com/user-attachments/assets/1dba2bf0-f3b8-43f8-9aeb-2dd2636ff2d3" />

- Sau đó danh sách tài khoản được phép kết nối sẽ hiện ra

<img width="430" height="477" alt="image" src="https://github.com/user-attachments/assets/1270279e-664e-4351-8f11-d7e89e7df00b" />

- Ở phần này, hãy đảm bảo rằng đã chọn tại mục **Allow connections only from computers…**

<img width="405" height="461" alt="image" src="https://github.com/user-attachments/assets/347a7dfa-b02c-4ff3-9361-c730ea5a9b27" />

## Bước 2: Cho phép nhiều User cùng truy cập bằng Remote Desktop

- Đầu tiên, nhấn nút **Start** sau đó nhập **gpedit.msc** rồi nhấn đúp vào để chọn/mở

<img width="927" height="738" alt="image" src="https://github.com/user-attachments/assets/664fc630-8b3b-40e6-bc4a-5dcc07ebe032" />

- Tìm lần lượt tới **Computer Configuration** => **Administrative Templates** => **Windows Components**

<img width="1389" height="851" alt="image" src="https://github.com/user-attachments/assets/3e336ec0-6998-4dd5-8246-9206e02b05a4" />

- Tiếp tục truy cập tới **Remote Desktop Services** => **Remote Desktop Session Host** => **Connections** rồi chuyển sang bên phải tìm kiếm mục **Limit number of connections**, nhấp đúp để mở

<img width="1383" height="846" alt="image" src="https://github.com/user-attachments/assets/bb1e7de6-360f-42e4-aba5-e383dd0ed711" />

- Chọn **Disable** rồi nhấn **OK** để không giới hạn số người dùng truy cập. Ngoài ra, ta cũng có thể chọn **Enable** để nhập số lượng người được phép truy cập ở phần **RD Maximum Connections allowed**. Chọn xong nhấn **Apply** => **OK **để lưu

<img width="683" height="638" alt="image" src="https://github.com/user-attachments/assets/683d5cd8-d607-45a7-b3ca-2ead36344a5a" />

- Tiếp tục chọn và mở **Restrict Remote Desktop Services…**

<img width="922" height="579" alt="image" src="https://github.com/user-attachments/assets/5fc9cfab-58c8-4d63-837f-68b289da824a" />

- Chọn **Disable** rồi nhấn **Apply** => **OK**

<img width="701" height="650" alt="image" src="https://github.com/user-attachments/assets/2d0a1fec-850d-47a0-9bc7-7ebaa6b93016" />

## Bước 3: Đăng ký dịch vụ để có thể cho phép nhiều hơn 2 User có thể truy cập Remote Desktop

> Phần này được thực hiện trên Windows Server.

- Nhấn **Start** rồi chọn **Server Manager**

<img width="779" height="623" alt="image" src="https://github.com/user-attachments/assets/c9af4d59-4af6-4c76-9a07-b45cd9aebd7e" />

- Tiếp theo, trên góc phải cửa sổ **Server Manager** -> chọn **Manage** => **Add Roles and Features**

<img width="1393" height="357" alt="image" src="https://github.com/user-attachments/assets/344b9170-fe4f-4f59-80ef-a06def6f1418" />

Ở cửa sổ mới này bạn nhấn Next để tiếp tục

<img width="793" height="560" alt="image" src="https://github.com/user-attachments/assets/5dfad60f-7a76-4181-b20e-469af6b90655" />

Bạn chọn Role-based or feature-based installation rồi nhấn Next

<img width="786" height="566" alt="image" src="https://github.com/user-attachments/assets/62ab6e1d-ee46-42c3-8b99-9f3cc1e113d8" />

Phần này để nguyên -> nhấn Next

<img width="789" height="563" alt="image" src="https://github.com/user-attachments/assets/692068aa-8d1c-43c4-883b-4e0c838872be" />

Bạn tick chọn Remote Desktop Services và tiếp tục nhấn Next

<img width="789" height="558" alt="image" src="https://github.com/user-attachments/assets/ea8e9d03-7f7b-4021-bb53-a3a66f00bcea" />

Nhấn Next

<img width="789" height="563" alt="image" src="https://github.com/user-attachments/assets/009073f0-ee39-4a23-9441-75f694553ca5" />

Bạn tick chọn 3 dịch vụ là: Remote Desktop Connection Broker, Remote Desktop Licensing, Remote Desktop Session Host. Sau mỗi lần tick sẽ có một bảng xác nhận hiện ra bạn hãy nhấn Add features

<img width="815" height="572" alt="image" src="https://github.com/user-attachments/assets/ddf689fd-b119-4bd7-a277-d4c8737ea13c" />

Bạn hãy tick vào Restart the destination server… để tự khởi động lại máy chủ sau khi cài xong dịch vụ. Nhấn Yes để xác nhận sau đó nhấn Install

<img width="783" height="575" alt="image" src="https://github.com/user-attachments/assets/f56ae5a6-3343-4ebc-b0bb-cfc7c25e337b" />

Bạn chờ máy chủ cài đặt xong và khởi động lại là hoàn thành thiết lập và có thể kết nối trên 2 máy tính qua Remote Desktop vào Windows Server.

<img width="794" height="573" alt="image" src="https://github.com/user-attachments/assets/a77274e7-d227-43da-af09-7b580901ae97" />

Chờ máy restart cập nhật rồi test với User vừa thêm.




