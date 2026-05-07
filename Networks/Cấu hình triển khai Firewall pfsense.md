Nghiên cứu triển khai và cấu hình hệ thống mạng công ty chia vlan, firewall
------


<img width="1310" height="823" alt="image" src="https://github.com/user-attachments/assets/14818493-7cbb-449b-bab1-993dfe8c1442" />
Hình ảnh sơ đồ mạng


# Bước 1: Cấu hình trên thiết bị pfsense

## 1/ pfsense sẽ tự động nhận số card mạng đang cắm vào thiết bị

## 2/  Nhập các thông số dựa theo card mạng

Enter the WAN interface name or 'a' for auto-detection

(vtnet0 vtnet1 vtnet2 vtnet3 or a): Chọn : **vtnet0** (ứng với wan VNPT)
- Enter the LAN interface name or 'a' for auto-detection

NOTE: this enables full Firewalling/NAT mode.
- (vtnet1 vtnet2 vtnet3 a or nothing if finished): Chọn : **vtnet2**(ứng với cổng lan có đầu ra mặc định truy cập vào pfsense là 192.168.1.1)

Enter the Optional 1 interface name or 'a' for auto-detection
- (vtnet1 vtnet3 a or nothing if finished): (**Để trống bấm phím Enter**)
Do you want to proceed [y|n]? **y**

## 3/ Sau khi cấu hình xong hãy truy cập vào PC cắm với cổng e2 với pfsense và set ip cho PC đó bất kì thuộc dải 192.168.1.0/24

# Bước 2: Cấu hình quay PPPoE và gộp băng thông dùng Load balancing cho 2 nhà mạng VNPT và FPT

## 1/ Interfaces → Assigments → Chọn **WAN** (vtnet0 là cổng WAN kết nối với model nhà mạng VNPT)

Tích chọn: Enable interface

Description: VNPT

IPv4 Configuration Type: PPPoE

Nhập thông tin Username và Password

Tích chọn:

- Block private networks and loopback addresses
- Block bogon networks

Bấm → Save → Apply Changes

## 2/ Interfaces → Assigments 

Tại Available network port: chọn vtnet1 (vtnet1 là cổng WAN kết nối với model nhà mạng FPT) → bấm Add

Tích chọn: Enable interface

Description: FPT

IPv4 Configuration Type: PPPoE

Nhập thông tin Username và Password

Tích chọn:
- Block private networks and loopback addresses
- Block bogon networks

Bấm → Save → Apply Changes

## 3/ Gộp nhà mạng VNPT và FPT vào group (Việc gộp này bao gồm cả khi 1 nhà mạng bị down thì chuyển sang nhà mạng còn lại để tiếp tục hoạt động)

Kiểm tra xem hiện tại đã nhập đúng các tài khoản PPPoE của các nhà mạng và đã hoạt động hay chưa?

Status → Gateways 

- Nếu cả 2 đã hiển thị trạng thái Status → Online rồi thì đã đúng,
- Nếu cả 2 hiển thị trạng thái Status → Pending thì kiểm tra xem phần Monitor có bị trùng IP hay không?
  - Nếu không thì kiểm tra lại phần tài khoản PPPoE đã nhập đúng hay chưa.
  - Nếu bị trùng IP thì tiến hành set lại Monitor.
    - Chọn System → Routing → Gateways
      - Chọn nhà mạng VNPT → bấm chọn Edit. Tại phần Monitor IP nhập: 8.8.8.8 → Save
      - Chọn nhà mạng FPT → bấm chọn Edit. Tại phần Monitor IP nhập: 8.8.4.4 → Save
      - Sau khi thực hiện 2 bước này xong thì bấm Apply Changes

Tiến hành gộp mạng VNPT và FPT

System → Gateway Groups → Add

- Group Name: LB_GW
- Gateway Priority:
  - VNPT_PPPOE → Tier 1→ Interface Address → Interface VNPT_PPPOE Gateway
  - FPT_PPPOE → Tier 1→ Interface Address → Interface VNPT_PPPOE Gateway
- Trigger Level: Packet Loss or High Latency
- Description: Load balencing wan vnpt fpt

→ Save → Apply Changes

Chọn lại Default gateway cho pfsense

System → Routing → Gateways 

Default gateway: 
- Default gateway IPv4: LB_GW (Load balancing wan vnpt fpt)
- Default gateway IPv6: None

→ Save → Apply Changes

Kiểm tra:
- Status → Gateways (Xem cả 2 nhà mạng Status đã Online hết hay chưa)
- Status → Interfaces (Xem VNPT Interface và FPT Interface → Status đã Up hết chưa)

## 4/ Bật DHCP Server cấp phát IP tự động cho các IP dải 192.168.1.10 → 192.168.1.250

Services → DHCP Server → LAN1

- Tích chọn: Enable DHCP server
- Range: 192.168.1.50 - 192.168.1.250
- Gateway: 192.168.1.1
- DNS servers: để trống (pfSense sẽ phát chính nó), hoặc điền 8.8.8.8, 8.8.4.4 nếu muốn ép DNS ngoài.
- (Tuỳ chọn) Domain home.arpa, NTP, Lease time…

→ Save / Apply.

# Bước 3: Tạo ra các VLAN tương ứng và mở các rule cần thiết để thông mạng trên Switch Layer 3 và Firewall pfSense

Cấu hình tạo ra 3 VLAN trên SWL3 
- VLAN 10 (IP 192.168.10.0/24) (Cổng kết nối từ SWL3 đến PfSense)
- VLAN 20 (IP 192.168.20.0/24) (Cổng kết nối từ SWL3 đến PC Users)
- VLAN 30 (IP 192.168.20.0/24) (Cổng kết nối từ SWL3 đến PC Kế toán)
- VLAN 40 (IP 192.168.20.0/24) (Cổng kết nối từ SWL3 đến Wifi)

Cắm cổng Console vào thiết bị

## 1/ bật L3 Routing trên switch
```
ena
conf t
hostname SWL3
no ip domain-lookup
ip routing
end
wr
```

## 2/ Tạo các vlan tương ứng
```
ena
conf t
vlan 10
  name VLAN10
vlan 20
  name VLAN20
vlan 30
  name VLAN30
vlan 40
  name VLAN40
exit
```

Kiểm tra các VLAN đã có hay chưa: 
```
ena
show vlan
```

## 3/ Gán IP cho các VLAN interface (SVI)
```
conf t
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shut
 
interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shut
 
interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shut
 
interface vlan 40
 ip address 192.168.40.1 255.255.255.0
 no shut
end
```

Kiểm tra xem các vlan đã có IP chưa:
```
show ip int bri
```

## 4/ Cấu hình các cổng kết nối
```
interface e0/1
 switchport mode access
 switchport access vlan 20
 no shut
 
interface e0/2
 switchport mode access
 switchport access vlan 30
 no shut
 
interface e0/3
 switchport mode access
 switchport access vlan 40
 no shut
end
```

## 5/ Cấu hình cổng kết nối đến pfSense (e0/0)
```
interface e0/0
 description Uplink to pfSense (LAN 192.168.10.254/24)
 switchport mode access
 switchport access vlan 10
 no shut
end
```

Kiểm tra:
```
show ip int bri
```

## 6/ Cấu hình Default route cho SWL3 về pfSense
```
conf t
ip route 0.0.0.0 0.0.0.0 192.168.10.254
end
wr
```

## 7/ Cấu hình DHCP server trên SWL3
```
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.20.250 192.168.20.254
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.30.250 192.168.30.254
ip dhcp excluded-address 192.168.40.1 192.168.40.10
ip dhcp excluded-address 192.168.40.250 192.168.40.254
 
 
ip dhcp pool VLAN20-POOL
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
 lease 1
 
ip dhcp pool VLAN30-POOL
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
 lease 1
 
ip dhcp pool VLAN40-POOL
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.1
 dns-server 8.8.8.8
 lease 1
 
end
wr
```

Kiểm tra:
```
show running-config
```

**LƯU Ý: MUỐN SỬ DỤNG SSH HOẶC TELNET ĐỂ KẾT NỐI TỚI SW L3 NÀY THÌ PHẢI ĐẶT USERNAM, PASSWORD TRƯỚC**

Tạo mật khẩu mới
```
enable
configure terminal
 
hostname SWL3
ip domain-name seta.local
 
crypto key generate rsa modulus 2048
 
username admin privilege 15 secret admin
 
line vty 0 4
 login local
 transport input ssh
 exec-timeout 15
 exit
 
ip ssh version 2
service password-encryption
 
end
write memory
```

# Bước 4: Khai báo tất cả các VLAN hiện có lên pfSense

LAN hiện có:
- VLAN10 (192.168.10.0/24) - vlan nối từ SWL3 lên pfSense
- VLAN20 (192.168.20.0/24) - vlan users
- VLAN30 (192.168.30.0/24) - vlan kế toán
- VLAN40 (192.168.40.0/24) - vlan wifi

## 1/ Thêm mới VLAN10

Interfaces → Assignments 

Available network ports: vtnet3 → Add

Chọn vtnet3 vừa thêm
- Tích chọn: Enable interface
- Description: LAN2
- IPv4 Configuration Type: Static IPv4
- IPv4 Address: 192.168.10.254 / 24

→ Save / Apply Changes

## 2/ Tạo Firewall Rule cho phép toàn bộ lưu lượng từ LAN10 này đến pfSense

(Rule này chỉ áp dụng cho LAN10 192.168.10.0/24)

Firewall → Rules → LAN2 → Add

- Action: Pass
- Interface: LAN2
- Address Family: IPv4
- Protocol: Any
- Source: any
- Destiantion: any
- Description: Allow all from VLAN10

→ Save

## 3/ Tạo Firewall Rule cho phép toàn bộ lưu lượng từ các VLAN 20/30/40 cũng đi đến pfSense thông qua LAN2

(Ta sẽ kết hợp sử dụng Alias và Rule)

Tạo Alias chứa các VLAN: Firewall → Aliases → Add → IP → Add

- Name: INTERNAL_LANS
- Type: Network(s)
- Thêm các IP sau tương ứng với các VLAN hiện có:
  - 192.168.20.0/24
  - 192.168.30.0/24
  - 192.168.40.0/24
- Description: VLAN20/30/40 behind SWL3

→ Save → Apply Changes

## 4/ Tạo Firewall Rule cho phép toàn bộ lưu lượng từ Alias (INTERNAL_LANS) này đến pfSense

Firewall → Rules → LAN2 → Add (Chọn mũi tên hướng lên trên để đặt rule này lên trên cùng)

- Action: Pass
- Interface: LAN2
- Address Famly: IPv4
- Protocol: Any
- Source: chọn Single host or alias → điền INTERNAL_LANS
- Destination: any

(Tuỳ chọn) Log để dễ theo dõi

- Description: Allow INTERNAL_LANS to anywhere

Save → Apply Changes.

**Rule này cho phép toàn bộ traffic từ 3 VLAN đi qua pfSense (Internet, site-to-site, OpenVPN, v.v.). Nếu đã có rule “LAN net → any” cũ chỉ áp dụng cho 192.168.10.0/24, ta có thể disable hoặc kéo xuống dưới.**

## 5/ Thêm Gateway cho VLAN 10

System → Routing → Static Routes → Add

- Interface: LAN2
- Address Family: IPv4
- Name: LAN2_GW
- Gateway: 192.168.10.1
- Description: Gateway to SWL3 

→ Save → Apply Changes

## 6/ Thêm Static Route cho các VLAN 20/30/40

System → Routing → Static Routes → Add

- Destination: 192.168.20.0 / 24
- Gateway: LAN2_GW - 192.168.10.1
- Description: SWL3_GW - 192.168.20.1

- Destination: 192.168.30.0 / 24
- Gateway: LAN2_GW - 192.168.10.1
- Description: SWL3_GW - 192.168.30.1

- Destination: 192.168.40.0 / 24
- Gateway: LAN2_GW - 192.168.10.1
- Description: SWL3_GW - 192.168.40.1

## 7/ Khai báo NAT Outbound để ra ngoài Internet

Firewall → NAT → Outbound

Tại Outbound NAT Mode chọn chế độ → Hybrid Outbound NAT rule generation. (Automatic Outbound NAT + rules below)

→ Save → Apply Changes

Tại Mappings → Bấm chọn Add có mũi tên hướng lên

(Tạo NAT Outbound cho Alias INTERNAL_LANS là gộp 3 VLAN vừa tạo)

- Interface: LAN2
- Address Family: IPv4
- Protocol: any
- Source / Type: Network
- Source network for the outbound NAT mapping: INTERNAL_LANS
- Destination: Any
- Address: Interface Address
- Description: NAT for INTERNAL_LANS

# Bước 5: Tạo Alias và các Rule liên quan

## 1/ Tạo Alias cho IP dải 192.168.1.0/24

- Name: Core_Net
- Description: IP Cre Server Local
- Type: Network(s)
- Network or FQDN: 192.168.1.0/24

→ Save

## 2/ Tạo Alias cho phép các IP local thuộc các VLAN20,30,40 được phép truy cập đến IP Server nội bộ

- Name: Internal_Server
- Description: Allow all from Server Local Admin
- Type: Host(s)
- Network or FQDN: 192.168.20.11 - IP bộ phận IT

→ Save

## 3/ Tạo Alias cho các Server dự án nội bộ IP 192.168.1.50 → 192.168.1.200

- Name: Project_Range
- Description: IP Network Project
- Type: Host(s)
- Network or FQDN: 192.168.1.50-192.168.1.200

→ Save

## 4/ Tạo Alias cho phép các IP cố định được phép truy cập đến Server Project

- Name: Internal_Server_Project
- Description: Allow IP from Server Project Local
- Type: Host(s)
- Network or FQDN: 192.168.20.20 - IP cho phép truy cập đến server project nội bộ

→ Save

## 5/ Tạo Rule chặn các VLAN20,30,40 truy cập đến Core_Net (IP 192.168.1.0/24)

Firewall → Rules → LAN2 → Add (Mũi tên hướng lên)

- Action: Block
- Interface: LAN2
- Address Family: IPv4
- Protocol: Any
- Source: Single host or alias → Internal_Lans (Alias chứa các VLAN20,30,40)
- Destination: Single host or alias → Core_Net (Alias chứa IP nội bộ 192.168.1.0/24)
- Description: Block Vlan 20,30,40 to Core_Net

→ Save

## 6/ Tạo Rule cho phép IP ADMIN từ các VLAN20,30,40 truy cập đến Core_Net (IP 192.168.1.0/24)

Firewall → Rules → LAN2 → Add (Mũi tên hướng lên)

- Action: Pass
- Interface: LAN2
- Address Family: IPv4
- Protocol: Any
- Source: Single host or alias → Internal_Server (Alias chứa IP bất kì thuộc VLAN20,30,40 được phép truy cập đến IP nội bộ)
- Destination: Single host or alias → Core_Net (Alias chứa IP nội bộ 192.168.1.0/24)
- Description: Allow IP Admin from Vlan 20,30,40 to Core_Net 

→ Save

## 7/ Tạo Rule cho phép IP nội bộ từ các VLAN20,30,40 (Internal_Server_Project) truy cập đến (Project_Range IP 192.168.1.0-192.168.1.200)

Firewall → Rules → LAN2 → Add (Mũi tên hướng lên)

- Action: Pass
- Interface: LAN2
- Address Family: IPv4
- Protocol: Any
- Source: Single host or alias → Internal_Server_Project (Alias chứa IP bất kì thuộc VLAN20,30,40 được phép truy cập đến IP nội bộ)
- Destination: Single host or alias → Project_Range (Alias chứa IP nội bộ 192.168.1.0/24)
- Description: Allow IP Project from Vlan 20,30,40 to Project_Range 

→ Save

> TỔNG KẾT: Thứ tự sắp sếp các Rule → LAN2 sẽ như sau:

- Allow IP Admin from Vlan 20,30,40 to Core_Net 
- Allow IP Project from Vlan 20,30,40 to Project_Range
- Block Vlan 20,30,40 to Core_Net
- Allow INTERNAL_LANS to anywhere
- Allow all from VLAN10

# Bước 6: Cấu hình OpenVPN và Rule truy cập

## 1/ Khởi tạo hạ tầng chứng thư cho OpenVPN (CA & Server Cert)

VPN → OpenVPN → Wizards

*Tạo VPN Admin 1196*

- Type Of Server: Local User Access
- → Next

- Create a New Certificate Authority (CA)
  - Descriptive Name: MyVPN_CA
  - Common Name: MyVPN_CA
- → Add new CA   

- Create a new Server Certificate
  - Descriptive Name: MyVPN_Server
  - Common Name: MyVPN_Server
- → Create new Certificate

- General OpenVPN Server Information
- Description: VPN Admin 1196
- Protocol: UDP on IPv4 only
- Interface: VNPT
- Local Port: 1196
- **IPv4 Tunnel Network**: 10.8.1.0/24
- **Redirect IPv4 Gateway: tích chọn** (ép toàn bộ Internet đi qua VPN, hoặc bỏ chọn nếu chỉ truy cập nội bộ)
- **IPv4 Local Network(s)**: 192.168.1.0/24,192.168.10.0/24,192.168.20.0/24,192.168.30.0/24,192.168.40.0/24
- → Next

- Tranffic from clients to server
  - Firewall Rule: Tích chọn để tạo sẵn rule tương ứng
- → Next → Finish

*Tương tự tạo cho VPN Project 1197*

- Type Of Server: Local User Access 
- → Next

- Certificate Authority: MyVPN_CA
- → Next

- Certificate: MyVPN_Server
- → Next

- General OpenVPN Server Information
  - Description: VPN Project 1197
  - Protocol: UDP on IPv4 only
  - Interface: VNPT
  - Local Port: 1197
  - IPv4 Tunnel Network: 10.8.2.0/24
  - Redirect IPv4 Gateway: tích chọn (ép toàn bộ Internet đi qua VPN, hoặc bỏ chọn nếu chỉ truy cập nội bộ)
  - IPv4 Local Network(s): 192.168.1.0/24
  - → Next

- Tranffic from clients to server
  - Firewall Rule: Tích chọn để tạo sẵn rule tương ứng
  - → Next → Finish

*Tương tự tạo cho VPN Project 1198*

- Type Of Server: Local User Access 
- → Next

- Certificate Authority: MyVPN_CA
- → Next

- Certificate: MyVPN_Server
- → Next

- General OpenVPN Server Information
- Description: VPN User 1198
- Protocol: UDP on IPv4 only
- Interface: VNPT
- Local Port: 1198
- **IPv4 Tunnel Network**: 10.8.3.0/24
- **Redirect IPv4 Gateway: tích chọn** (ép toàn bộ Internet đi qua VPN, hoặc bỏ chọn nếu chỉ truy cập nội bộ)
- **IPv4 Local Network(s)**: 192.168.20.0/24,192.168.30.0/24,192.168.40.0/24
- → Next

- Tranffic from clients to server
  - Firewall Rule: Tích chọn để tạo sẵn rule tương ứng
- → Next → Finish

<img width="947" height="578" alt="image" src="https://github.com/user-attachments/assets/afd1afe0-4a7a-4482-9b4f-9f835cce616f" />

## 2/ Tạo Rule tương ứng cho các quyền Admin, Project, User

Firewall → Rules → OpenVPN

Add (Chọn mũi tên hướng lên) → Tạo Rule Admin 

- Action: Pass
- Interface: OpenVPN
- Address Family: IPv4
- Protocol: Any
- Source: Network → 10.8.1.0 / 24
- Destination: any
- Log: Có thể tích nếu muốn có log
- Description: Allow Admin full access
- → Save

Add (Chọn mũi tên hướng lên) → Tạo Rule Project 

- Action: Pass
- Interface: OpenVPN
- Address Family: IPv4
- Protocol: Any
- Source: Network → 10.8.2.0 / 24
- Destination: Single host or alias → Project_Range
- Log: Có thể tích nếu muốn có log
- Description: Allow Project to Project_Range
- → Save

Add (Chọn mũi tên hướng lên) → Tạo Rule User

- Action: Pass
- Interface: OpenVPN
- Address Family: IPv4
- Protocol: Any
- Source: Network → 10.8.3.0 / 24
- Destination: Single host or alias → Internal_Lans
- Log: Có thể tích nếu muốn có log
- Description: Allow User to VLAN20,30,40
- → Save

<img width="1158" height="418" alt="image" src="https://github.com/user-attachments/assets/e236ab9c-4210-4c74-a408-3e0db7a8481f" />

## 3/ Cấu hình Rule cho WAN VNPT

Đã được thêm tự động từ winzard

<img width="1158" height="420" alt="image" src="https://github.com/user-attachments/assets/fa1ee957-38f0-43eb-8a97-c021d383138d" />

## 4/ Tạo NAT Outbound cho các mạng VPN (nếu client cần ra Internet)

Hện đang dùng Hybrid Outbound NAT. Thêm 3 mapping:

Firewall → NAT → Outbound → Add (↑)

- Interface: VNPT
- Address Family: IPv4
- Protocol: any
- Source network: 10.8.1.0/24
- Destination: Any
- Translation / Address: Interface address
- Description: NAT for VPN_Admin
- → Save & Apply

Firewall → NAT → Outbound → Add (↑)

- Interface: VNPT
- Address Family: IPv4
- Protocol: any
- Source network: 10.8.2.0/24
- Destination: Any
- Translation / Address: Interface address
- Description: NAT for VPN_Project
- → Save & Apply

Firewall → NAT → Outbound → Add (↑)

- Interface: VNPT
- Address Family: IPv4
- Protocol: any
- Source network: 10.8.3.0/24
- Destination: Any
- Translation / Address: Interface address
- Description: NAT for VPN_Users
- → Save & Apply

<img width="1149" height="619" alt="image" src="https://github.com/user-attachments/assets/364e0371-a4b9-46cd-b6a8-546939fa630a" />

5/ Tạo user + xuất cấu hình Client

System → User Manager → Add

- Username: <Điền thông tin tương ứng>
- Password: <Điền thông tin tương ứng>
- Full name: <Điền thông tin tương ứng>
- Certificate: Tích chọn (Click to create a user certificate)
- Descriptive name: <Điền thông tin tương ứng>
- → Save

Sau khi đã tạo User xong thì tiến hành Export OpenVPN của User này

VPN → OpenVPN → Client Export

Chọn quyền Export cho User này:

Remote Access Server:
- VPN Admin 1196 UDP4:1196
- VPN Project 1197 UDP4:1197
- VPN User 1198 UDP4:1198

Kéo xuống cuối tại phần: OpenVPN Clients

Tìm đến User cần export → Chọn tải xuống bằng "Most Clients"

Nhớ cấp User và Password đúng khi mình tạo User

Nếu đang test tạm trên lab thì tắt tính năng này đi sẽ VPN bình thường

Interfaces → VNPT 

Bỏ tích: 
- Block private networks and loopback addresses
- Block bogon networks

→ Save → Apply Change

Có thể làm tương tự nếu muốn OpenVPN đi qua WAN FPT

<img width="1219" height="349" alt="image" src="https://github.com/user-attachments/assets/188f6f61-9485-47c0-bfb3-4c6e5fc7c711" />

# Bước 7: Tạo NAT cho các server đi ra ngoài Internet (Chỉ định NAT bằng WAN FPT)

## 1/ Thêm Outbound NAT cho 192.168.1.200 trên FPT

Firewall → NAT → Outbound → Add (mũi tên lên)

- Interface: FPT
- Address Family: IPv4
- Protocol: any
- Source / Type: Network → 192.168.1.200
- Destination: Any
- Translation / Address: Interface address (FPT)
- Description: NAT 192.168.1.200 via FPT

→ Save → Apply Changes.

(Đây là rule NAT thủ công tương tự cách đã NAT cho các mạng khác trong hướng dẫn trước.)

## 2/ Tạo Firewall rule (policy routing) ép lưu lượng ra FPT

IP 192.168.1.200 nằm ở LAN 192.168.1.0/24, vì vậy đặt rule ở tab LAN:

Firewall → Rules → LAN → Add (kéo lên trên rule Allow LAN to any hiện có)

- Action: Pass
- Interface: LAN
- Address Family: IPv4
- Protocol: any
- Source: Single host or alias -> 192.168.1.200
- Destination: any
- Description: Route 192.168.1.200 out FPT
- Advanced Options → Gateway: chọn FPT_DHCP (gateway của WAN FPT)

→ Save → Apply Changes.

# Bước 8: Chỉ định cho 1 LAN trong dải mạng thông qua 1 WAN ra ngoài Internet

-----------------

Alias và Rule hiện đang áp dụng

<img width="1146" height="709" alt="image" src="https://github.com/user-attachments/assets/fafd55b1-18bc-4ce4-846a-ca52e72522c5" />

Có thể Disable Rule này đi vì đã có Rule Allow Internal_Lans anywhere bao gồm rồi

<img width="1155" height="420" alt="image" src="https://github.com/user-attachments/assets/0bc8701c-4d8b-453e-b3cd-8e174e96a480" />

<img width="1155" height="420" alt="image" src="https://github.com/user-attachments/assets/80c70481-4d52-4193-a636-072ac842a9a9" />

<img width="1151" height="434" alt="image" src="https://github.com/user-attachments/assets/d6071b0a-f40d-4764-865f-f75401ec53ff" />

<img width="1156" height="487" alt="image" src="https://github.com/user-attachments/assets/70618b34-b2f3-4662-865c-69cf0d46cd96" />


Có thể Disable Rule này đi vì đã có Rule Allow Internal_Lans anywhere bao gồm rồi

<img width="1175" height="567" alt="image" src="https://github.com/user-attachments/assets/67f27a84-c216-4b04-b3a7-f74d1dafd3fa" />

<img width="1157" height="416" alt="image" src="https://github.com/user-attachments/assets/3985aca6-5ea2-444c-b83a-4b0b7f1db1d7" />
