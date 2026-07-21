1. Giới thiệu
1.1 Mục đích

Remote Desktop Services (RDS) là giải pháp của Microsoft cho phép nhiều người dùng truy cập đồng thời vào một máy chủ Windows Server thông qua giao thức Remote Desktop Protocol (RDP). Mỗi người dùng sẽ được cấp một phiên làm việc (Session) độc lập với các tài nguyên và môi trường làm việc riêng.

Tài liệu này hướng dẫn triển khai mô hình RDS Multi-Session trên Windows Server 2019, bao gồm:

Cài đặt Remote Desktop Services.
Cấu hình Remote Desktop Licensing.
Cho phép nhiều người dùng đăng nhập đồng thời.
Quản lý Session.
Kiểm thử và xử lý sự cố.
Áp dụng các Best Practices trong môi trường doanh nghiệp.
1.2 Phạm vi áp dụng

Tài liệu áp dụng cho các môi trường sau:

Phòng Lab.
Doanh nghiệp nhỏ và vừa.
Trung tâm đào tạo.
Máy chủ ứng dụng nội bộ.
Máy chủ làm việc từ xa.
1.3 Đối tượng sử dụng
System Administrator
System Engineer
IT Support
Sinh viên thực hành Windows Server
2. Tổng quan Remote Desktop Services
2.1 Remote Desktop Services là gì?

(Giữ nguyên nội dung của bạn.)

Sau đó bổ sung thêm:

Đặc điểm của RDS
Hỗ trợ nhiều người dùng đăng nhập đồng thời.
Mỗi người dùng có Desktop và Session riêng biệt.
Chia sẻ tài nguyên phần cứng của cùng một máy chủ.
Hỗ trợ triển khai ứng dụng tập trung.
Giảm chi phí đầu tư máy trạm.

2.2 So sánh Remote Desktop và RDS
| Tiêu chí   | Remote Desktop    | Remote Desktop Services                         |
| ---------- | ----------------- | ----------------------------------------------- |
| Mục đích   | Quản trị máy chủ  | Cung cấp Desktop cho nhiều User                 |
| Số Session | Tối đa 2          | Không giới hạn (phụ thuộc License và phần cứng) |
| License    | Không cần RDS CAL | Cần RDS CAL                                     |
| Người dùng | Administrator     | User thông thường                               |
| Môi trường | Quản trị          | Doanh nghiệp                                    |

2.3 Thành phần của RDS
Bổ sung bảng đầy đủ hơn:
| Thành phần             | Chức năng                              |
| ---------------------- | -------------------------------------- |
| RD Session Host        | Máy chủ chạy Desktop Session           |
| RD Licensing           | Quản lý bản quyền RDS                  |
| RD Connection Broker   | Điều phối và cân bằng Session          |
| RD Gateway             | Truy cập RDP qua HTTPS                 |
| RD Web Access          | Cổng Web truy cập RemoteApp và Desktop |
| RD Virtualization Host | Triển khai VDI với Hyper-V             |

3. Kiến trúc triển khai

Giữ nguyên sơ đồ.

Sau mỗi sơ đồ nên giải thích.

Ví dụ:

3.1 Mô hình Lab

Đặc điểm:

Một máy chủ duy nhất.
RD Session Host và RD Licensing cài trên cùng một Server.
Không triển khai RD Gateway.
Phù hợp học tập và thử nghiệm.
3.2 Mô hình Enterprise

Đặc điểm:

Tách riêng từng vai trò.
Có Active Directory.
Có RD Gateway.
Có RD Connection Broker.
Có Load Balancing.
4. Điều kiện triển khai

Bổ sung:

Hệ điều hành hỗ trợ
Windows Server 2016
Windows Server 2019
Windows Server 2022
Yêu cầu mạng
IP tĩnh.
DNS phân giải chính xác.
Đồng bộ thời gian (NTP).
Firewall mở cổng 3389/TCP.
5. Chuẩn bị Server

Bổ sung thêm:

Cập nhật Windows

Khuyến nghị cài đặt các bản cập nhật mới nhất trước khi triển khai RDS để tránh lỗi liên quan đến dịch vụ và tăng cường bảo mật.

Kiểm tra tên máy
hostname
Kiểm tra địa chỉ IP
ipconfig
Kiểm tra DNS
nslookup localhost
6. Cài đặt Remote Desktop Services

Ngoài giao diện GUI, bổ sung thêm PowerShell.

Install-WindowsFeature RDS-RD-Server,RDS-Licensing -IncludeManagementTools
7. Kiểm tra Role

Ngoài Server Manager.

Có thể kiểm tra bằng:

Get-WindowsFeature *RDS*
8. RD Licensing

Bổ sung:

Grace Period

Windows Server cho phép sử dụng RDS trong thời gian khoảng 120 ngày mà chưa cần cài đặt RDS CAL.

Sau thời gian này, máy chủ sẽ từ chối tạo các phiên Remote Desktop mới nếu chưa được cấu hình License Server hợp lệ.

9. Activate License

Bổ sung:

Có ba phương thức:

Automatic Connection
Web Browser
Telephone

Khuyến nghị:

Automatic Connection
10. RDS CAL

Bổ sung giải thích.

Per User

Một người dùng có thể đăng nhập trên nhiều thiết bị.

Ví dụ:

Laptop

Desktop

Thin Client

vẫn chỉ cần một License.

Per Device

Một thiết bị được nhiều người dùng sử dụng.

Ví dụ:

Máy tính phòng LAB
11. Licensing Mode

Bổ sung cách kiểm tra.

Get-RDLicenseConfiguration
12. Tạo User

Ngoài GUI.

Có thể tạo bằng CMD.

net user user01 Password@123 /add

Hoặc PowerShell.

New-LocalUser
13. Phân quyền

Bổ sung:

Kiểm tra Group.

net localgroup "Remote Desktop Users"

Thêm User.

net localgroup "Remote Desktop Users" user01 /add
14. Group Policy

Ngoài các Policy đã có.

Bổ sung:

Encryption Level
High
Network Level Authentication
Enabled
Keep Alive
Enabled
Clipboard Redirection
Enabled

hoặc

Disabled

theo chính sách doanh nghiệp.

15. Firewall

Bổ sung:

Kiểm tra Rule.

Get-NetFirewallRule *Remote*
16. Kiểm thử

Bổ sung:

Kiểm tra CPU.

taskmgr

Kiểm tra RAM.

resmon

Kiểm tra Event.

Event Viewer

↓

Applications and Services Logs

↓

Microsoft

↓

Windows

↓

TerminalServices
17. Quản lý Session

Bổ sung:

Liệt kê Session.

query session

Ngắt Session.

logoff 2

Reset Session.

rwinsta 2
18. Troubleshooting

Ngoài các lỗi đã có.

Bổ sung thêm:

Không Remote được

Kiểm tra:

Test-NetConnection SERVERNAME -Port 3389
Session bị treo
reset session ID
License Server không nhận

Kiểm tra:

Get-RDLicenseConfiguration
Event Log
Applications and Services Logs

↓

TerminalServices
19. Best Practices

Đây là phần rất quan trọng nhưng tài liệu hiện tại còn thiếu. Nên bổ sung các khuyến nghị sau:

Không sử dụng tài khoản Administrator để người dùng làm việc hàng ngày.
Quản lý người dùng thông qua Active Directory thay vì Local User trong môi trường doanh nghiệp.
Sử dụng RD Gateway để truy cập từ Internet, không mở trực tiếp cổng 3389/TCP.
Áp dụng Network Level Authentication (NLA) để tăng cường bảo mật.
Cài đặt chứng chỉ SSL/TLS hợp lệ cho các dịch vụ RDS.
Thiết lập chính sách tự động đăng xuất các Session không hoạt động.
Theo dõi CPU, RAM và số lượng Session để tránh quá tải máy chủ.
Thực hiện sao lưu định kỳ cấu hình và dữ liệu người dùng.
20. Phụ lục
Các lệnh CMD thường dùng
query user
query session
logoff ID
rwinsta ID
hostname
ipconfig
gpupdate /force
Các lệnh PowerShell
Get-WindowsFeature *RDS*

Get-NetFirewallRule *Remote*

Get-RDLicenseConfiguration

Install-WindowsFeature RDS-RD-Server,RDS-Licensing -IncludeManagementTools
