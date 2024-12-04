Cấu hình chuyển thiết bị Onu nhà mạng Viettel thành Bridge Mode và quay PPPoE cấu hình trên Router Draytek 2926
----

## Bước 1: Vào modem onu nhà mạng Viettel ( Sử dụng wifi hoặc kết nối mạng dây RJ45 với laptop)
- Pass wifi là:
  - pass: 1234567890a
    
## Bước 2: Truy cập vào địa chỉ :  192.168.1.1 
- Username: admin
- Password: DSNW27cc5e60 (Có pass phía dưới thiết bị)

## Bước 3: Cấu hình dịch vụ Internet PPPoE
- Màn hình Easy Page Setup:
  - WAN setting:
    - username: vtv_8830240
    - password: 4S7Lyj
    VLAN mặc định 11, VLAN tòa nhà TechnoPark 102
→ Hoặc liên hệ với bên nhà mạng để cấu hình Bridge Mode trước.
Onu nhà mạng (Cổng LAN1 hỗ trợ 1Gbps) → Draytek (Cổng WAN 1) → Cổng LAN1 (Draytek dùng để cấu hình quay PPPoE)

## Bước 4: Cấu hình Draytek 2926
- Truy cập địa chỉ 192.168.1.1
  - Username: admin
  - Password: admin
- Cấu hình Internet
  - WAN → Internet Access 
  - Tại WAN1 → Access Mode, chọn PPPoE → Sau đó chọn Details Page
    - Tab PPPoE chọn Enable
      - Username/Password: vtv_8830240 / 4S7Lyj
      - MTU: 1438 hoặc lấy từ nhà cung cấp
      - Sau đó OK → hiện ra thông báo yêu cầu khởi động lại Vigor → Nhấn OK

## Bước 5: Kiểm tra đã có Internet hay chưa
Đăng nhập Vigor → Online Status → Chọn mục Physical Connection (hiện thông tin cổng WAN1)
Tại đây chữ màu xanh hiện lên với đầy đủ IP WAN → cấu hình thành công


Note: Tại sao cần phải cấu hình như thế này?
-	Từ thiết bị nhà mạng (Quang  Module Onu  Điện): đã có thể cấp phát ip tự động bằng DHCP  đã có thể sử dụng được luôn.
-	Cấu hình Bridge trên thiết bị Module nhà mạng sẽ biến Module nhà mạng thành cầu nối thông qua nó để kết nối tới thiết bị cân bằng tải Router Draytek Vigor 2926.
-	Khi cấu hình Bridge cho thiết bị mạng ta sẽ không vào được internet nữa (lúc này module nhà mạng chỉ có nghiệm vụ là dùng để chuyển đổi quang  điện) và cần phải vào thiết bị cân bằng tải Router Draytek Vigor 2926 quay PPPoE.
-	PPPoE (Point-to-Point Protocol over Ethernet) - được thiết kế để quản lý cách truyền dữ liệu qua mạng Ethernet (mạng cáp) và cho phép kết nối một máy chủ duy nhất được phân chia giữa nhiều máy khách, sử dụng Ethernet.
-	Trên Router Daytek sẽ thực hiện nhiệm vụ quay pppoe, dhcp cấp IP, NAT, chia VLAN (nếu có). Tại đây sẽ có hỗ trợ cân bằng tải load balancing sẽ thực hiện cân bằng tải duy trì trang thái ổn định tốc độ mạng ( dễ dàng quản lý cấp phát địa chỉ Ip cho từng máy, có thể cấu hình nhiều chức năng ví dụ như VPN).
