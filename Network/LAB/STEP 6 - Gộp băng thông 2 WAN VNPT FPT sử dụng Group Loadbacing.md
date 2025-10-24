Hướng dẫn gộp băng thông 2 WAN VNPT, FPT sử dụng Group Loadbacing.
----

1) Sử dụng PPPoE cho 2 WAN (VNPT và FPT)

* Interfaces → [WAN_VNPT]

- Tích chọn Enable interface
- Description: VNPT
- IPv4 Configuration Type: PPPoE
- IPv6 Configuration Type: none
- Username: [adminwanvnpt]
- Password: 
- Tích chọn: (vì đây là interface hướng Internet)
  - Block private networks and loopback addresses
  - Block bogon networks

<img width="1011" height="658" alt="image" src="https://github.com/user-attachments/assets/75b33290-bbde-4eaa-a7d9-a8cc9197d163" />

<img width="1007" height="663" alt="image" src="https://github.com/user-attachments/assets/3184ce8a-c2d7-4a72-bbfe-737359e1c9d2" />

<img width="1008" height="659" alt="image" src="https://github.com/user-attachments/assets/f1c60c7f-b08d-4d14-ae36-38ae1e025f5c" />

<img width="1001" height="620" alt="image" src="https://github.com/user-attachments/assets/266f9574-7e89-44d3-a4ae-0e7cd547c7ca" />

* Interfaces → [WAN_FPT]

- Tích chọn Enable interface
- Description: FPT
- IPv4 Configuration Type: PPPoE
- IPv6 Configuration Type: none
- Username: [adminwanfpt]
- Password: 
- Tích chọn: (vì đây là interface hướng Internet)
  - Block private networks and loopback addresses
  - Block bogon networks

<img width="1003" height="658" alt="image" src="https://github.com/user-attachments/assets/0b30b07b-ac53-4b6b-96c8-92cb9b1d8027" />

<img width="1003" height="659" alt="image" src="https://github.com/user-attachments/assets/591121b4-430a-486b-ab25-bba58c51531f" />

<img width="1007" height="619" alt="image" src="https://github.com/user-attachments/assets/fcabe6ba-6e6c-4793-9570-653b272c31db" />

-----

Sau khi Connect, mỗi WAN sẽ có gateway động (dpinger tạo sẵn).

Lưu ý: Nhiều modem PPPoE không trả lời ping ở gateway. 
Khi đó nên đặt Monitor IP là IP công cộng tin cậy (ví dụ 8.8.8.8 hoặc 1.1.1.1) cho từng gateway.

System → Routing → Gateways → (sửa từng WAN)

Monitor IP: ví dụ 1.1.1.1 cho VNPT, 8.8.8.8 cho FPT. (Hoặc 8.8.8.8 cho VNPT, 8.8.4.4 cho FPT)

Để Default Gateway IPv4 = Automatic (pfSense sẽ tự chọn theo policy rule về sau).

<img width="1006" height="663" alt="image" src="https://github.com/user-attachments/assets/0f0acd82-d9f3-4fab-b5d2-cbe22a3610a8" />

<img width="1005" height="659" alt="image" src="https://github.com/user-attachments/assets/60464cec-a592-4bee-901a-36d4502f0278" />


<img width="1004" height="659" alt="image" src="https://github.com/user-attachments/assets/f916d841-d696-4770-b538-7f86c9cb1458" />

<img width="1008" height="660" alt="image" src="https://github.com/user-attachments/assets/04f6b0cf-d335-4b9e-aba7-5b5c825050cf" />

2) Tạo Gateway Group (Load-balancing)

System → Routing → Gateway Groups → Add

- Group Name: LB_WAN
- Gateway priority:

<table>
  <tr>
    <th>Gateway</th><th>Tier</th><th>Virtual IP</th><th>Description</th>
  </tr>
  <tr>
    <td>VNPT_PPPOE</td><td>Tier 1</td><td>Interface Address</td><td>Interface VNPT_PPPOE Gateway</td>
  </tr>
  <tr>
    <td>FPT_PPPOE</td><td>Tier 1</td><td>Interface Address</td><td>Interface FPT_PPPOE Gateway</td>
  </tr>
  <tr>
    <td>LAN2_GW</td><td>Nerver</td><td>Interface Address</td><td>Gateway to SWL3</td>
  </tr>
</table>

- Trigger Level: Packet Loss or High Latency (→ ổn định hơn “Member Down” vì PPPoE nhiều khi vẫn “up” nhưng mất gói/độ trễ cao.)

-> Save/Apply

Tier 1 + Tier 1 = ECMP load-balancing. Nếu 1 WAN “down” (theo tiêu chí trigger), toàn bộ lưu lượng sẽ tự dạt sang WAN còn lại.

3) NAT cho Multi-WAN

Firewall → NAT → Outbound

Chọn Hybrid Outbound NAT (hoặc Manual nếu bạn muốn kiểm soát hoàn toàn).

Alias hiện đang sử dụng

<img width="959" height="434" alt="image" src="https://github.com/user-attachments/assets/e960ac7f-47f6-44c0-8c48-1600e2b0289a" />

Tối ưu lại NAT -> Outbound

Xóa các dòng VLAN20/30/40 riêng (nếu không cần behavior riêng). Giữ/ tạo theo thứ tự:

VNPT — CORE_NET → Interface address

FPT — CORE_NET → Interface address

VNPT — INTERNAL_LANS → Interface address ← thay cho 3 dòng 20/30/40

FPT — INTERNAL_LANS → Interface address ← thêm dòng này (bạn đang thiếu)

(Tuỳ chọn) VNPT — VPN_NETS hoặc FPT — VPN_NETS

Chỉ cần nếu bạn muốn subnet VPN đi Internet qua một WAN.

Nếu VPN chỉ truy cập nội bộ, không cần NAT cho VPN_NETS.

Giữ phần Automatic Rules (ISAKMP 500/4500) ở dưới cùng như hiện tại.

Thông số mỗi mapping:
- Interface: VNPT hoặc FPT
- Source: CORE_NET / INTERNAL_LANS (alias)
- Destination: * (any)
- Translation / Address: Interface address
- Static Port: OFF (mặc định).
  - Chỉ bật khi có ứng dụng cần giữ nguyên port nguồn (một số SIP/VoIP, game).
  - IPsec đã có rule auto Static Port riêng.

<img width="943" height="636" alt="image" src="https://github.com/user-attachments/assets/0ad842de-9e2d-4474-a5d6-2551db948fdc" />

<img width="943" height="653" alt="image" src="https://github.com/user-attachments/assets/07d58e5f-946b-4fc1-8fd6-04cecd1890da" />

<img width="945" height="626" alt="image" src="https://github.com/user-attachments/assets/bd3f3371-a59d-4fa7-a9b2-c708191dc1f2" />

<img width="938" height="646" alt="image" src="https://github.com/user-attachments/assets/d891a093-7d35-49f1-8431-4d6fd49490c5" />

<img width="941" height="637" alt="image" src="https://github.com/user-attachments/assets/127d0845-97a1-4afe-8c9e-538e9b393f20" />

<img width="947" height="629" alt="image" src="https://github.com/user-attachments/assets/35a965db-8745-484c-bfa8-ba28280f566f" />

<img width="944" height="633" alt="image" src="https://github.com/user-attachments/assets/00e8a00b-affd-4659-9cd3-18a878a798fb" />

<img width="945" height="577" alt="image" src="https://github.com/user-attachments/assets/02ab16ff-51f9-45e6-b79c-e8ec91c463b9" />


4) Rule điều khiển load-balancing (Policy Routing)

Firewall → Rules → LAN (mạng 192.168.1.0/24)



































