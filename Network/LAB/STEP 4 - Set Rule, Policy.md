1. Chặn tất cả lưu lượng từ VLAN 20/30/40 → 192.168.1.0/24

2. Tạo 3 OpenVPN server (admin / project / user) và viết rule đúng phạm vi

3. NAT outbound cho các subnet VPN để ra Internet

----

1, Set Alias
----

Vào Firewall → Aliases → IP tạo các alias sau:

- INTERNAL_LANS (Type: Network(s)): (Đây là Alias nhóm các Vlan 20, 30, 40)

<img width="1008" height="656" alt="image" src="https://github.com/user-attachments/assets/14ade843-f2ff-4912-966b-bb4974c06038" />

`192.168.20.0/24`, `192.168.30.0/24`, `192.168.40.0/24`

- CORE_NET (Type: Network): (Đây là Alias cho các IP thuộc dải `192.168.1.0/24`)

<img width="1002" height="659" alt="image" src="https://github.com/user-attachments/assets/57570430-b940-44b2-ba1b-3d44f988ce52" />

`192.168.1.0/24`

- PROJECT_RANGE (Type: Host(s) hoặc Network(s) — pfSense cho phép ghi range): (Đây là Alias dùng để cho phép các IP này sử dụng để làm gì từ `1.50 -> 1.200`)

`192.168.1.50-192.168.1.200`

- (sẽ dùng sau) VPN_NETS (Type: Network(s)): (Đây là Alias cho các IP của VPN)

`10.8.1.0/24`, `10.8.2.0/24`, `10.8.3.0/24`

Save → Apply Changes.
