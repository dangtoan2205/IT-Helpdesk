Đặt lại thời gian gia hạn (Grace Period) 120 ngày của RDS trên Windows Server 2016 và 2019
----

Khi cài đặt Windows Server 2016 hoặc Windows Server 2019 (cũng như các phiên bản Windows Server trước đó), mặc định có thể mở 2 phiên Remote Desktop để quản trị máy chủ.

**Remote Desktop Services (RDS)**, máy chủ sẽ **hỗ trợ nhiều phiên Remote Desktop đồng thời (Multi-Session)**.

Sau khi cài đặt vai trò RDS, Microsoft sẽ cấp cho **120 ngày dùng thử (Grace Period)** để có thể cấu hình và kích hoạt RDS CAL (Remote Desktop Services Client Access License).

Sau khi 120 ngày Grace Period kết thúc, khi kết nối Remote Desktop đến máy chủ, sẽ gặp thông báo lỗi tương tự như sau:


> **Remote Desktop Services has exceeded the grace period and cannot accept new client connections.**


<img width="560" height="149" alt="image" src="https://github.com/user-attachments/assets/777d8a7c-28cd-4463-a370-e53168def780" />

Lúc này có hai lựa chọn:

- Cài đặt lại Windows Server, để có thêm một chu kỳ 120 ngày mới.
- Đặt lại Grace Period, giúp tiếp tục sử dụng mà không cần cài lại hệ điều hành.

-> Lựa chọn thứ hai rõ ràng sẽ nhanh hơn và ít tốn công sức hơn.

Cách đặt lại thời gian dùng thử 120 ngày của RDS trên Windows Server 2016 và 2019
----

Để đặt lại **Grace Period**, sẽ sử dụng **Registry Editor**.

Quá trình đặt lại chỉ gồm 3 bước: 

**1/ Thay đổi quyền (Permissions) của khóa Registry RCM\GracePeriod**

**2/ Xóa giá trị Registry Timebomb**

**3/ Khởi động lại máy chủ**

## Bước 1: Mở khóa Registry GracePeriod

Mở Registry Editor (regedit) và truy cập đến:

```
Computer
 └─ HKEY_LOCAL_MACHINE
     └─ SYSTEM
         └─ CurrentControlSet
             └─ Control
                 └─ Terminal Server
                     └─ RCM
                         └─ GracePeriod
```

<img width="1269" height="627" alt="image" src="https://github.com/user-attachments/assets/65f050b9-ba86-4f91-8a86-4ab218883f46" />


## Bước 2: Thay đổi quyền của khóa GracePeriod

Nhấp chuột phải vào **GracePeriod** và chọn **Permissions...**

<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/befa4963-7007-48d1-8582-cefedeaa9cf3" />

> Theo mặc định, ngay cả nhóm **Administrators** cũng không có quyền chỉnh sửa khóa này.
> Vì vậy, trước tiên chỉnh sửa lại quyền sở hữu **(Take Ownership)** của khóa **Registry**.

Trong cửa sổ **Permissions for SYSTEM**

Chọn **Advanced**

<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/9032299e-0dfc-43bc-b432-4661cdb2cb9c" />

Tiếp theo:

Nhấn **Change** tại mục **Owner**

<img width="767" height="520" alt="image" src="https://github.com/user-attachments/assets/16c03e58-7922-4286-8704-bfb7373dbe97" />

Chọn:

- **Administrators**
- hoặc tài khoản người dùng muốn sử dụng.

Sau đó đánh dấu:

- **Replace owner on subcontainers and objects**
- **Replace all child object permission entries**

Sau đó nhấn **OK**.

<img width="767" height="521" alt="image" src="https://github.com/user-attachments/assets/c8e8c3b5-698e-40ef-b8ee-4d1e876b01ff" />

Xác nhận việc thay đổi quyền khi Windows yêu cầu.

<img width="409" height="185" alt="image" src="https://github.com/user-attachments/assets/479e599c-ce1c-4b89-8c8c-7663f00c12da" />

Sau khi đã là chủ sở hữu của khóa **Registry**, quay lại cửa sổ **Permissions**.

Cấp các quyền sau cho nhóm **Administrators**:

**Full Control**

Nhấn **Apply** → **OK**.

<img width="363" height="450" alt="image" src="https://github.com/user-attachments/assets/c6bbf07b-dbc7-485a-8bbe-15f303799579" />

## Bước 3: Xóa giá trị Timebomb

Trong khóa:

```
GracePeriod
```
Ta sẽ thấy một giá trị có tên tương tự:

```
L$RTMTIMEBOMB
```
(hoặc còn được gọi là Timebomb).

Nhấp chuột phải vào giá trị này và chọn:

```
Delete
```
Sau đó xác nhận việc xóa.

<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/0b39656b-d056-4c69-b8b6-6ec2c81fb6c2" />

## Bước 4: Khởi động lại máy chủ

Sau khi xóa khóa **Registry**:

**Khởi động lại Windows Server**.

Sau khi máy chủ khởi động lại, **Grace Period của RDS sẽ được đặt lại về 120 ngày** và có thể tiếp tục sử dụng RDS.

<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/8b5e18fb-60a3-4f2d-8fde-d128281256d7" />

Kết luận
----

Việc đặt lại 120 ngày Grace Period của Remote Desktop Services trên:
```
Windows Server 2019
Windows Server 2016
Windows Server 2012
Windows Server 2012 R2
```
là một thao tác khá đơn giản. Chỉ cần xóa giá trị Registry "Timebomb" sau khi đã thay đổi quyền truy cập.



