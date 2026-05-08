# Lab cấu hình HA trên 2 thiết bị pfSense và Stackwise trên EVE-NG

# 1/ Cấu hình HA trên 2 thiết bị pfSense

| pfSense 1 (MASTER)                                                                                                                                                                   | pfSense 2 (BACKUP) |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------ |
| **Bước 1: Setup trên thiết bị pfSense**                                                                                                                                              | Tương tự           |
| - Should VLANs be set up now[y/n]? Chọn `y`                                                                                                                                          |                    |
| - Enter the WAN interface name or 'a' for auto-detection: Chọn `vtnet0` (ứng với IPS mạng VNPT đang cắm vào port 0 trên thiết bị)                                                  |                    |
| - Enter the LAN interface name or 'a' for auto-detection: Chọn `vtnet5` (ứng với port 5 từ thiết bị cắm với switch L2 dùng để cắm với PC dùng để cấu hình trên giao diện thiết bị) |                    |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 2: Setup trên giao diện WEB** | **Bước 2: Setup trên giao diện WEB** |
| (Mặc định LAN của pfSense sẽ có IP là `192.168.1.1`) | (Mặc định LAN của pfSense sẽ có IP là `192.168.1.1`) |
| (Khi đã kết nối các thiết bị sw + PC thì tiến hành kiểm tra IP hiện tại của máy để đưa IP hiện tại của máy thành `192.168.1.100` trùng với IP của pfSense) | (Khi đã kết nối các thiết bị sw + PC thì tiến hành kiểm tra IP hiện tại của máy để đưa IP hiện tại của máy thành `192.168.1.100` trùng với IP của pfSense) |
| **pfSense SETUP:** | **pfSense SETUP:** |
| - Accept | - Accept |
| - Step 0 of 9 → Next | - Step 0 of 9 → Next |
| - Step 1 of 9 → Next | - Step 1 of 9 → Next |
| **Step 2 of 9** | **Step 2 of 9** |
| - Hostname: `pfSense-Master` | - Hostname: `pfSense-Backup` |
| - → Next | - → Next |
| **Step 3 of 9** | **Step 3 of 9** |
| - Timezone: `Asia/Ho_Chi_Minh` | - Timezone: `Asia/Ho_Chi_Minh` |
| - → Next | - → Next |
| **Step 4 of 9** | **Step 4 of 9** |
| - SelectdType: Chọn PPPoE hoặc DHCP như lab | - SelectdType: Chọn PPPoE hoặc DHCP như lab |
| - Block private networks from entering via WAN | - Block private networks from entering via WAN |
| - Block non-Internet routed networks from entering via WAN | - Block non-Internet routed networks from entering via WAN |
| - → Next | - → Next |
| **Step 5 of 9** | **Step 5 of 9** |
| - LAN IP Address: `192.168.1.10` | - LAN IP Address: `192.168.1.20` |
| - Subnet Mask: `24` | - Subnet Mask: `24` |
| - → Next | - → Next |
| **Step 6 of 9** | **Step 6 of 9** |
| - Admin Password | - Admin Password |
| - Admin Password AGAIN | - Admin Password AGAIN |
| - → Next | - → Next |
| **Step 7 of 9** | **Step 7 of 9** |
| - Chọn Reload | - Chọn Reload |
| (Sau đó sẽ sử dụng IP `192.168.1.10` này để truy cập vào pfSense Master) | (Sau đó sẽ sử dụng IP `192.168.1.20` này để truy cập vào pfSense Backup) |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 3: Tạo SYNC Interface** | **Bước 3: Tạo SYNC Interface** |
| (Trên Lab đang sử dụng cổng `e2` từ pfSense-Master để kết nối với cổng `e2` của pfSense-Backup) | (Trên Lab đang sử dụng cổng `e2` từ pfSense-Master để kết nối với cổng `e2` của pfSense-Backup) |
| (Để liên kết thì ta cần sử dụng 1 dây LAN với VLAN riêng để kết nối) | (Để liên kết thì ta cần sử dụng 1 dây LAN với VLAN riêng để kết nối) |
| **Tại giao diện WEB** | **Tại giao diện WEB** |
| - Chọn `Interfaces → Assignments` | - Chọn `Interfaces → Assignments` |
| - Available network port: Chọn `vtnet2` → Add | - Available network port: Chọn `vtnet2` → Add |
| - Chọn vào `OPT1` | - Chọn vào `OPT1` |
| - Enable interface: Tích chọn | - Enable interface: Tích chọn |
| - Description: đổi từ `OPT1 → SYNC` | - Description: đổi từ `OPT1 → SYNC` |
| - IPv4 Configuration Type: `Static IPv4` | - IPv4 Configuration Type: `Static IPv4` |
| - IPv4 Address: `10.20.10.251/28` | - IPv4 Address: `10.20.10.252/28` |
| - → Save → Apply Changes | - → Save → Apply Changes |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 4: Tạo Rule cho phép đồng bộ trên cổng SYNC** | **Bước 4: Tạo Rule cho phép đồng bộ trên cổng SYNC** |
| - Chọn `Firewall → Rules → SYNC` | - Chọn `Firewall → Rules → SYNC` |
| - Chọn Add mũi tên hướng lên | - Chọn Add mũi tên hướng lên |
| - Action: `Pass` | - Action: `Pass` |
| - Interface: `SYNC` | - Interface: `SYNC` |
| - Address Family: `IPv4` | - Address Family: `IPv4` |
| - Protocol: `Any` | - Protocol: `Any` |
| - Source: `SYNC subnets` hoặc `SYNC net` | - Source: `SYNC subnets` hoặc `SYNC net` |
| - Destination: `Any` | - Destination: `Any` |
| - Description: `Allow SYNC Configuration` | - Description: `Allow SYNC Configuration` |
| - → Save → Apply Changes | - → Save → Apply Changes |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 5: Cấu hình HA** | |
| - Chỉ cấu hình trên pfSense Master | |
| - `System → High Availability` | |
| - Synchronize states: Tích chọn | |
| - Synchronize Interface: `SYNC` | |
| - Filter Host ID: `251` | |
| - pfsync Synchronize Peer IP: `10.20.10.252` | |
| - Synchronize Config to IP: `10.20.10.252` | |
| - Remote System Username: `admin` | |
| - Remote System Password: `pfsense` | |
| - Synchronize admin: Tích chọn | |
| - Select options to sync: `Toggle All` | |
| **Toggle All:** | |
| - Bỏ chọn `DHCP Relay settings` | |
| - Bỏ chọn `DHCPv6 Relay settings` | |
| - → Save | |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 6: Cấu hình CARP Virtual IPs cho LAN** | |
| - Chỉ cấu hình trên pfSense Master | |
| - `Firewall → Virtual IPs → Add` | |
| - Type: `CARP` | |
| - Interface: `LAN` | |
| - Address(es): `192.168.1.1/24` | |
| - Virtual IP Password: `pfsense` | |
| - VHID Group: `1` | |
| - Advertising frequency Base: `1` | |
| - Advertising frequency Skew: `0` | |
| - Description: `Virtual IP CARP LAN` | |
| - → Save → Apply Changes | |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 7: Đặt DHCP Server để nhận Gateway là 192.168.1.1** | |
| - `Services → DHCP Server → LAN` | |
| - Other Options → Gateway: `192.168.1.1` | |
| - → Save | |

---

# 2/ Cấu hình thêm interface cho IPS của FPT

## Các mục sẽ tự sync

- Firewall rules, schedules, aliases
- NAT
- Virtual IPs (CARP)
- DHCP Server
- IPsec, OpenVPN
- Static routes
- DNS Resolver / Forwarder
- User manager, auth servers

## Các mục không sync

- Interface assignment (`vtnet0/vtnet1`, VLAN, ...)
- Tên interface
- Mapping port vật lý

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 1: Đặt lại tên WAN VNPT** | **Bước 1: Đặt lại tên WAN VNPT** |
| - `Interfaces → Assignments` | - `Interfaces → Assignments` |
| - Chọn Interface `WAN` | - Chọn Interface `WAN` |
| - Description: đổi từ `WAN → VNPT` | - Description: đổi từ `WAN → VNPT` |
| - IPv6 Configuration Type: `None` | - IPv6 Configuration Type: `None` |
| - → Save → Apply Changes | - → Save → Apply Changes |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 2: Thêm Interface cho IPS mạng FPT** | Tương tự Master |
| - `Interfaces → Assignments` | |
| - Available network ports: Chọn `vtnet1` | |
| - Chọn `OPT2` để chỉnh sửa | |
| - Enable interface: Tích chọn | |
| - Description: `FPT` | |
| - IPv4 Configuration Type: `DHCP` hoặc `PPPoE` | |
| - IPv6 Configuration Type: `None` | |
| - Block private networks from entering via WAN | |
| - Block non-Internet routed networks from entering via WAN | |
| - → Save → Apply Changes | |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 3: Gộp 2 IPS thành 1 đường mạng** | |
| **Kiểm tra Gateway** | |
| - `Status → Gateways` | |
| - Nếu Online → OK | |
| - Nếu Pending → kiểm tra PPPoE hoặc Monitor IP | |
| **Monitor IP** | |
| - `System → Routing → Gateways` | |
| - VNPT Monitor IP: `8.8.8.8` | |
| - FPT Monitor IP: `8.8.4.4` | |
| **Tạo Gateway Group** | |
| - `System → Routing → Gateway Groups → Add` | |
| - Group Name: `LB_GW` | |
| - VNPT_PPPOE → Tier 1 | |
| - FPT_PPPOE → Tier 1 | |
| - Trigger Level: `Packet Loss or High Latency` | |
| - Description: `Load balancing wan vnpt fpt` | |
| - → Save → Apply Changes | |
| **Đặt Default Gateway** | |
| - `System → Routing → Gateways` | |
| - Default gateway IPv4: `LB_GW` | |
| - Default gateway IPv6: `None` | |
| - → Save → Apply Changes | |

---

# 3/ CẤU HÌNH TRÊN pfSense (Router-on-a-stick)

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 1: Tạo VLAN trên pfSense** | **Bước 1: Tạo VLAN trên pfSense** |
| `Interfaces → Assignments → VLANs → Add` | `Interfaces → Assignments → VLANs → Add` |
| VLAN20 → `vtnet3 (e3)` → Tag `20` | VLAN20 → `vtnet3 (e3)` → Tag `20` |
| VLAN30 → `vtnet3 (e3)` → Tag `30` | VLAN30 → `vtnet3 (e3)` → Tag `30` |
| VLAN40 → `vtnet3 (e3)` → Tag `40` | VLAN40 → `vtnet3 (e3)` → Tag `40` |
| VLAN90 → `vtnet3 (e3)` → Tag `90` | VLAN90 → `vtnet3 (e3)` → Tag `90` |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 2: Gán VLAN thành interfaces** | **Bước 2: Gán VLAN thành interfaces** |
| OPT3 → VLAN20 → `192.168.20.251/24` | OPT3 → VLAN20 → `192.168.20.252/24` |
| OPT4 → VLAN30 → `192.168.30.251/24` | OPT4 → VLAN30 → `192.168.30.252/24` |
| OPT5 → VLAN40 → `192.168.40.251/24` | OPT5 → VLAN40 → `192.168.40.252/24` |
| OPT6 → VLAN90 → `192.168.90.251/24` | OPT6 → VLAN90 → `192.168.90.252/24` |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 3: Tạo CARP VIP cho từng VLAN** | Rule Backup tự động đồng bộ từ Master |
| `Firewall → Virtual IPs → Add → Type: CARP` | |
| VLAN20 → `192.168.20.1/24` → VHID `20` | |
| VLAN30 → `192.168.30.1/24` → VHID `30` | |
| VLAN40 → `192.168.40.1/24` → VHID `40` | |
| VLAN90 → `192.168.90.1/24` → VHID `90` | |
| Password giống nhau cho dễ nhớ | |
| - → Save → Apply Changes | |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 4: Tạo DHCP Server cho từng VLAN** | DHCP Server Backup tự động đồng bộ từ Master |
| VLAN20 → Range `192.168.20.20 - 192.168.20.200` → Gateway `192.168.20.1` | |
| VLAN30 → Range `192.168.30.20 - 192.168.30.200` → Gateway `192.168.30.1` | |
| VLAN40 → Range `192.168.40.20 - 192.168.40.200` → Gateway `192.168.40.1` | |
| VLAN90 → Range `192.168.90.20 - 192.168.90.200` → Gateway `192.168.90.1` | |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 5: Tạo Firewall Rules cho từng VLAN** | Rule sẽ tự đồng bộ sang Backup |
| VLAN20 → Allow VLAN20 to Internet | |
| VLAN30 → Allow VLAN30 to Internet | |
| VLAN40 → Allow VLAN40 to Internet | |
| VLAN90 → Allow VLAN90 to Internet | |
| Action: `Pass` | |
| Address Family: `IPv4` | |
| Protocol: `Any` | |
| Destination: `any` | |
| → Apply Changes | |
| **Lưu ý:** | |
| - Có thể chặn VLAN20 truy cập VLAN90/VLAN30/VLAN40 | |
| - pfSense đọc rule từ trên xuống dưới | |
| - Rule Block phải đặt phía trên Allow | |
| - Nên dùng Alias để dễ quản lý | |

# 4/ CẤU HÌNH TRÊN SWL3-Core-1 (Hoạt động như L2 switch)

## Mục tiêu

- Chỉ dùng SVI VLAN10 để giao tiếp pfSense
- Tất cả port về Router-on-a-stick đều trunk
- Các port về PC → access VLAN tương ứng

| Bước | Nội dung |
| --- | --- |
| **Bước 1: Tạo VLAN trên switch** | ```cisco<br>ena<br>conf t<br><br>hostname SWL3-Core-1<br>no ip domain-lookup<br>ip routing<br><br>! Tạo VLAN<br><br>vlan 10<br>name TRANSIT<br><br>vlan 20<br>name USER<br><br>vlan 30<br>name FINANCE<br><br>vlan 40<br>name WIFI<br><br>vlan 90<br>name SERVER<br>``` |
| **Bước 2: Tạo SVI duy nhất cho VLAN10** | ```cisco<br>interface vlan 10<br> ip address 192.168.10.1 255.255.255.0<br> no shutdown<br>```<br><br>**Không tạo SVI cho VLAN20/30/40/90**<br>→ Để pfSense làm router |
| **Bước 3: Cấu hình trunk về pfSense (e3)** | ```cisco<br>interface gi0/0<br> switchport mode trunk<br> switchport trunk allowed vlan 20,30,40,80,10<br>``` |
| **Bước 4: Cấu hình access cho từng VLAN** | **VLAN20 (Users)**<br><br>```cisco<br>interface gi0/4<br> switchport mode access<br> switchport access vlan 20<br>```<br><br>**VLAN30 (Ke Toan)**<br><br>```cisco<br>interface gi0/5<br> switchport mode access<br> switchport access vlan 30<br>```<br><br>**VLAN40 (Wifi)**<br><br>```cisco<br>interface gi0/6<br> switchport mode access<br> switchport access vlan 40<br>```<br><br>**VLAN90 (Server)**<br><br>```cisco<br>interface gi0/7<br> switchport mode access<br> switchport access vlan 90<br>``` |

---

# 5/ STATIC ROUTE trên SW và pfSense

| Thiết bị | Cấu hình |
| --- | --- |
| SW Core → Router (pfSense) | ```cisco<br>ip route 0.0.0.0 0.0.0.0 192.168.10.254<br>``` |
| pfSense | Không cần static route nội bộ vì VLAN nằm trên pfSense → routing tự động |

---

# 6/ CARP cho VLAN10 / LANCORE (mạng transit 192.168.10.0/24)

## Mục tiêu

- SWL3 SVI VLAN10: `192.168.10.1/24`
- pfSense Master (LANCORE): `192.168.10.252/24`
- pfSense Backup (LANCORE): `192.168.10.253/24`
- CARP VIP: `192.168.10.254/24`
- SWL3 dùng `192.168.10.254` làm default gateway

Trong pfSense lab, interface đó đang tên `LANCORE – vtnet3`

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 1: Đặt IP thật cho LANCORE trên 2 pfSense** | **Bước 1: Đặt IP thật cho LANCORE trên 2 pfSense** |
| Vào `Interface → Thêm mới interface vtnet3` (LAN xuống SWL3) | Vào `Interface → Thêm mới interface vtnet3` (LAN xuống SWL3) |
| - Enable interface | - Enable interface |
| - Description: `LANCORE` | - Description: `LANCORE` |
| - IPv4 Configuration Type: `Static IPv4` | - IPv4 Configuration Type: `Static IPv4` |
| - IPv4 Address: `192.168.10.252/24` | - IPv4 Address: `192.168.10.253/24` |
| - IPv4 Upstream gateway: `None` | - IPv4 Upstream gateway: `None` |
| - IPv6: `None` | - IPv6: `None` |
| - Save → Apply Changes | - Save → Apply Changes |

---

| pfSense 1 (MASTER) | pfSense 2 (BACKUP) |
| --- | --- |
| **Bước 2: Tạo CARP VIP cho VLAN10 (LANCORE)** | Backup sẽ tự sync |
| Làm trên `pfSense Master` | |
| `Firewall → Virtual IPs → Add` | |
| - Type: `CARP` | |
| - Interface: chọn `LANCORE` | |
| - Address type: `Single address` | |
| - Address(es): `192.168.10.254/24` | |
| - Virtual IP Password: ví dụ `pfsensecarp` | |
| - VHID Group: `10` | |
| - Advertising frequency Base: `1` | |
| - Advertising frequency Skew: `0` (Master) | |
| - Description: `CARP Transit VLAN10` | |
| - Save → Apply Changes | |
| Sau khi sync sang Backup có thể chỉnh Skew Backup thành `100` | |
| Trên Backup vào `Status → CARP` kiểm tra VIP `192.168.10.254` ở trạng thái `BACKUP` | |
| Trên Master VIP `192.168.10.254` phải là `MASTER` | |

---

# 7/ Cấu hình Switch SWL3-Core-1

| Bước | Cấu hình |
| --- | --- |
| **Bước 1: Tạo VLAN** | ```cisco<br>ena<br>conf t<br><br>vlan 10<br> name TRANSIT<br>exit<br><br>vlan 20<br> name USER<br>exit<br><br>vlan 30<br> name FINANCE<br>exit<br><br>vlan 40<br> name WIFI<br>exit<br><br>vlan 90<br> name SERVER<br>exit<br>``` |
| **Bước 2: SVI VLAN10 (transit)** | ```cisco<br>interface vlan 10<br> ip address 192.168.10.1 255.255.255.0<br> no shutdown<br>``` |
| **Bước 3: Hai port kết nối lên pfSense (e0/0 & e0/1)** | ```cisco<br>interface e0/0<br> switchport trunk encapsulation dot1q<br> switchport mode trunk<br> switchport trunk native vlan 10<br> switchport trunk allowed vlan 10,20,30,40,90<br> no shutdown<br>exit<br><br>interface e0/1<br> switchport trunk encapsulation dot1q<br> switchport mode trunk<br> switchport trunk native vlan 10<br> switchport trunk allowed vlan 10,20,30,40,90<br> no shutdown<br>exit<br>```<br><br>```text<br>Cả hai port e0/0 và e0/1 đều là trunk, không phải access VLAN10<br>``` |
| **Bước 4: Các port xuống user / server** | ```cisco<br>! VLAN20 – User<br>interface e0/2<br> switchport mode access<br> switchport access vlan 20<br>exit<br><br>! VLAN30 – Kế toán<br>interface e0/3<br> switchport mode access<br> switchport access vlan 30<br>exit<br><br>! VLAN40 – Wifi<br>interface e1/0<br> switchport mode access<br> switchport access vlan 40<br>exit<br><br>! VLAN90 – Server<br>interface e1/1<br> switchport mode access<br> switchport access vlan 90<br>``` |
| **Bước 5: Switch SVI VLAN10 default-route** | ```cisco<br>ip route 0.0.0.0 0.0.0.0 192.168.10.254<br>``` |

# 8/ Tạo các Alias

| Alias | Config |
| --- | --- |
| **Alias cho ADMIN**<br><br>IP: `192.168.1.0/24`<br><br>(Đây là “tất cả LAN/VLAN nội bộ” – để PC quản trị có thể đi khắp nơi.) | - Name: `ADMIN_NETS`<br>- Description: `All internal admin networks`<br>- Type: `Network(s)`<br>- Network or FQDN:<br>  - `192.168.1.0/24`<br>  - `192.168.10.0/24`<br>  - `192.168.20.0/24`<br>  - `192.168.30.0/24`<br>  - `192.168.40.0/24`<br>  - `192.168.90.0/24`<br><br>→ Save |
| **Alias cho VLAN 90 (Server local)** | - Name: `LOCAL_SERVERS`<br>- Description: `Local servers VLAN90`<br>- Type: `Network(s)`<br>- Network or FQDN: `192.168.90.0/24`<br><br>→ Save |
| **Alias cho phép các IP cố định được phép truy cập đến ADMIN_NETS** | - Name: `HOST_ADMIN`<br>- Description: `Allow IP local to Admin Local`<br>- Type: `Host(s)`<br>- Network or FQDN:<br>  - `192.168.20.100`<br><br>→ Save |
| **Alias gom các VLAN 20,30,40** | - Name: `USER_VLANS`<br>- Description: `User VLANs 20,30,40`<br>- Type: `Network(s)`<br>- Network or FQDN:<br>  - `192.168.20.0/24`<br>  - `192.168.30.0/24`<br>  - `192.168.40.0/24`<br><br>→ Save |
| **Alias cho phép các IP cố định được phép truy cập đến LOCAL_SERVER** | - Name: `HOST_SERVER`<br>- Description: `Allow IP local to Server Local`<br>- Type: `Host(s)`<br>- Network or FQDN:<br>  - `192.168.30.100`<br><br>→ Save |

---

# 10/ Viết rule trên interface VLAN 20,30,40,90

# Firewall → Rules → VLAN20

| Rule | Config |
| --- | --- |
| **Rule 1:**<br><br>Cho phép `HOST_ADMIN` vào toàn bộ `ADMIN_NETS` | - Action: `Pass`<br>- Interface: `VLAN20`<br>- Address Family: `IPv4`<br>- Protocol: `Any`<br>- Source:<br>  - Type: `Single host or alias`<br>  - Value: `HOST_ADMIN`<br>- Destination:<br>  - Type: `Single host or alias`<br>  - Value: `ADMIN_NETS`<br>- Description: `Allow HOST_ADMIN to ADMIN_NETS`<br><br>👉 Máy `192.168.20.100` sẽ dùng rule này, có full quyền vào tất cả mạng nội bộ |
| **Rule 2:**<br><br>Chặn toàn bộ VLAN20 vào các mạng nội bộ (kể cả server) | - Action: `Block`<br>- Interface: `VLAN20`<br>- Address Family: `IPv4`<br>- Protocol: `Any`<br>- Source: `VLAN20 net`<br>- Destination:<br>  - Type: `Single host or alias`<br>  - Value: `ADMIN_NETS`<br>- Description: `Block VLAN20 to ADMIN_NETS`<br><br>👉 Tất cả host khác trong VLAN20 sẽ không truy cập được:<br>`192.168.1.0/24, 10.0/24, 20.0/24, 30.0/24, 40.0/24, 80.0/24` (trừ HOST_ADMIN đã match Rule 1) |
| **Rule 3:**<br><br>Cho phép VLAN20 ra Internet | - Action: `Pass`<br>- Interface: `VLAN20`<br>- Address Family: `IPv4`<br>- Protocol: `Any`<br>- Source: `VLAN20 net`<br>- Destination: `any`<br>- Description: `Allow VLAN20 to Internet`<br><br>👉 Nhờ rule 2 chỉ block đích = `ADMIN_NETS`, nên đi Internet vẫn OK |

## Nên để thứ tự như sau (từ trên xuống)

1. **PASS – HOST_ADMIN → ADMIN_NETS**
   - Source: `HOST_ADMIN`
   - Destination: `ADMIN_NETS`
   - Mục đích: máy `192.168.20.100` có quyền đi khắp LAN/VLAN nội bộ

2. **BLOCK – VLAN20 net → ADMIN_NETS**
   - Source: `VLAN20 net`
   - Destination: `ADMIN_NETS`
   - Mục đích: tất cả máy VLAN20 khác bị chặn đi các mạng nội bộ

3. **PASS – VLAN20 net → any** (Allow VLAN20 to Internet)
   - Source: `VLAN20 net`
   - Destination: `any`

→ Xóa rule PASS `"VLAN20 net → any"` đang nằm ở DÒNG ĐẦU (rule này thừa và phá hết chính sách).

---

# Rules cho VLAN30 (kế toán + 1 host quản lý server)

## Firewall → Rules → VLAN30

| Rule | Config |
| --- | --- |
| **Rule 1:**<br><br>Cho `HOST_SERVER` được vào `LOCAL_SERVERS` | - Action: `Pass`<br>- Interface: `VLAN30`<br>- Address Family: `IPv4`<br>- Protocol: `Any`<br>- Source: `HOST_SERVER (192.168.30.100)`<br>- Destination: `LOCAL_SERVERS (192.168.80.0/24)`<br>- Description: `Allow HOST_SERVER to LOCAL_SERVERS` |
| **Rule 2:**<br><br>Chặn VLAN30 vào `LOCAL_SERVERS` (trừ HOST_SERVER) | - Action: `Block`<br>- Interface: `VLAN30`<br>- Address Family: `IPv4`<br>- Protocol: `Any`<br>- Source: `VLAN30 net`<br>- Destination: `LOCAL_SERVERS`<br>- Description: `Block VLAN30 to LOCAL_SERVERS`<br><br>👉 `192.168.30.100` đã match Rule 1 nên không bị rule 2 chặn; các máy VLAN30 khác sẽ bị block khi vào VLAN90 |
| **Rule 3:**<br><br>Chặn VLAN30 vào các mạng nội bộ khác (`ADMIN_NETS`) | - Action: `Block`<br>- Source: `VLAN30 net`<br>- Destination: `ADMIN_NETS`<br>- Description: `Block VLAN30 to ADMIN_NETS`<br><br>(Rule này block luôn truy cập đến mạng admin, transit, VLAN20, VLAN40...) |
| **Rule 4:**<br><br>Cho VLAN30 ra Internet | - Action: `Pass`<br>- Source: `VLAN30 net`<br>- Destination: `any`<br>- Description: `Allow VLAN30 to Internet` |

## Thứ tự nên là

1. **PASS – HOST_SERVER → LOCAL_SERVER**
   - Source: `HOST_SERVER`
   - Destination: `LOCAL_SERVER`
   - Cho phép `192.168.30.100` truy cập server

2. **BLOCK – VLAN30 net → LOCAL_SERVER**
   - Source: `VLAN30 net`
   - Destination: `LOCAL_SERVER`
   - Chặn tất cả máy VLAN30 khác vào server

3. **BLOCK – VLAN30 net → ADMIN_NETS**
   - Source: `VLAN30 net`
   - Destination: `ADMIN_NETS`
   - Chặn VLAN30 đi đến các mạng nội bộ khác

4. **PASS – VLAN30 net → any** (Allow VLAN30 to Internet)

→ Xóa rule PASS `"VLAN30 net → any"` đang ở DÒNG ĐẦU (thừa).

---

# Rules cho VLAN40 (Wifi / Guest)

## Firewall → Rules → VLAN40

| Rule | Config |
| --- | --- |
| **Rule 1:**<br><br>Chặn VLAN40 vào `LOCAL_SERVERS` (server) | - Action: `Block`<br>- Source: `VLAN40 net`<br>- Destination: `LOCAL_SERVERS`<br>- Description: `Block VLAN40 to LOCAL_SERVERS` |
| **Rule 2:**<br><br>Chặn VLAN40 vào các mạng nội bộ khác (`ADMIN_NETS`) | - Action: `Block`<br>- Source: `VLAN40 net`<br>- Destination: `ADMIN_NETS`<br>- Description: `Block VLAN40 to ADMIN_NETS` |
| **Rule 3:**<br><br>Cho VLAN40 ra Internet | - Action: `Pass`<br>- Source: `VLAN40 net`<br>- Destination: `any`<br>- Description: `Allow VLAN40 to Internet` |

## Thứ tự nên là

1. **BLOCK – VLAN40 net → LOCAL_SERVER**
   - Source: `VLAN40 net`
   - Destination: `LOCAL_SERVER`

2. **BLOCK – VLAN40 net → ADMIN_NETS**
   - Source: `VLAN40 net`
   - Destination: `ADMIN_NETS`

3. **PASS – VLAN40 net → any** (Allow VLAN40 to Internet)

→ Xóa rule PASS `"VLAN40 net → any"` đang ở DÒNG ĐẦU (thừa).

---

# Rules cho VLAN90 (Server)

## Firewall → Rules → VLAN90

| Rule | Config |
| --- | --- |
| **Rule 1:**<br><br>Cho server nói chuyện với `ADMIN_NETS` (quản trị) | - Action: `Pass`<br>- Source: `LOCAL_SERVERS`<br>- Destination: `ADMIN_NETS`<br>- Description: `Allow servers to ADMIN_NETS (mgmt)` |
| **Rule 2:**<br><br>Chặn server trả lời trực tiếp về `USER_VLANS` | - Action: `Block`<br>- Source: `LOCAL_SERVERS`<br>- Destination: `USER_VLANS`<br>- Description: `Block servers to USER_VLANS` |
| **Rule 3:**<br><br>Cho server ra Internet (nếu cần) | - Action: `Pass`<br>- Source: `LOCAL_SERVERS`<br>- Destination: `any`<br>- Description: `Allow servers to Internet` |

## Thứ tự đúng nên là

1. **PASS – LOCAL_SERVER → ADMIN_NETS**
   - Cho phép server nói chuyện với mạng quản trị/admin

2. **BLOCK – LOCAL_SERVER → USER_VLANS**
   - Không cho server nói chuyện trực tiếp với VLAN20/30/40

3. **PASS – VLAN90 net → any** (Allow VLAN90 to Internet)
   - Hoặc nếu muốn chặt hơn:
     - PASS `LOCAL_SERVER → DNS/Internet` cụ thể thay vì any

→ Xóa rule PASS `"LOCAL_SERVER → USER_VLANS"` đang ở DÒNG ĐẦU, vì nó phủ hết rule Block ngay bên dưới.

---

# LANCORE (VLAN10 – transit)

## Firewall → Rules → LANCORE

Tab LANCORE hiện chưa có rule → mặc định block hết.

Để switch VLAN10 (`192.168.10.1`) quản trị / ping / truy cập GUI pfSense được, nên thêm tối thiểu:

| Rule | Config |
| --- | --- |
| **Rule 1:** | - Action: `Pass`<br>- Interface: `LANCORE`<br>- Address Family: `IPv4`<br>- Protocol: `Any`<br>- Source: `LANCORE net`<br>- Destination: `any`<br>- Description: `Allow LANCORE`<br><br>Nếu muốn chặt hơn: đặt `Destination = This firewall` để chỉ cho phép truy cập chính pfSense từ mạng transit |
