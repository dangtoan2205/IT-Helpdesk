Cấu hình pfSense
----
Interface

WAN: 192.168.1.1/24 (đi Internet)

LAN (nối xuống SWL3 VLAN 10): 192.168.10.254/24

----

1) LAN Interface

<img width="1025" height="669" alt="image" src="https://github.com/user-attachments/assets/5dff51ca-6988-4e39-aae2-34b4e490be30" />

<img width="1009" height="662" alt="image" src="https://github.com/user-attachments/assets/2ecb0b82-37ae-4d55-9e03-4ac0ea5994e7" />

Interfaces -> Assignments

Chọn LAN2 là đường mạng LAN xuống SWL3

<img width="1007" height="658" alt="image" src="https://github.com/user-attachments/assets/30178f6b-808b-4b37-8164-21c6afba9c61" />

<img width="1019" height="657" alt="image" src="https://github.com/user-attachments/assets/44f9cc2d-1885-45ad-b17d-f2fdc89bb56d" />

<img width="1005" height="656" alt="image" src="https://github.com/user-attachments/assets/8e09ab12-249a-4c5c-bc46-88022aee8be7" />

- Enable interface
- Description: LAN2
- IPv4 Configuration Type: Static IPv4
- IPv4 Address: 192.168.10.254/24
- Save & Apply.

2) Tạo firewall rule cho interface e2 trên pfSense

Mặc định pfSense chặn toàn bộ lưu lượng vào từ interface mới, nên cần mở rule để LAN2 có thể giao tiếp và NAT.

<img width="1024" height="691" alt="image" src="https://github.com/user-attachments/assets/c046e19d-28c4-4e1b-ae1a-6fb8de5bbd6e" />

Firewall -> Rules -> LAN2
----

<img width="1006" height="656" alt="image" src="https://github.com/user-attachments/assets/9e96cd27-1fcf-4ffe-b006-cf8eb3b031c5" />

<img width="1004" height="666" alt="image" src="https://github.com/user-attachments/assets/665a01e1-098a-4b9c-9893-8494bb6f04c0" />

Rule chỉ áp dụng cho 192.168.10.0/24

- Action: Pass
- Interface: LAN2
- Address Family: IPv4
- Protocol: Any
- Source: any
- Destination: any
- Description: Allow all from VLAN10
-> Save

Ta cần khai báo các Rule cho các VLAN. Vì lưu lượng từ VLAN 20/30/40 cũng đi vào pfSense qua interface LAN, ta cần cho phép các mạng này

Ta sẽ kết hợp Alias và Rule để gộp các rule này lại cho gọn.

Tạo Alias gộp các VLAN

<img width="1024" height="624" alt="image" src="https://github.com/user-attachments/assets/1d7ab06b-b4f8-4f1d-831a-d90abedda126" />

Firewall -> Aliases -> IP -> Add

<img width="1009" height="664" alt="image" src="https://github.com/user-attachments/assets/24eeddcf-96af-4b7a-938f-276d6987397f" />

- Name: INTERNAL_LANS
- Type: Network(s)
- Thêm các IP sau tương ứng với các VLAN hiện có:
  - 192.168.20.0/24
  - 192.168.30.0/24
  - 192.168.40.0/24
 - Description: VLAN20/30/40 behind SWL3
-> Save

Tạo 1 Rule để sử dụng Alias này

<img width="1022" height="622" alt="image" src="https://github.com/user-attachments/assets/f9236fdb-31b5-4ce7-a642-bfcecfa01e3e" />

Firewall → Rules → LAN → Add (mũi tên lên để đặt trên cùng).

<img width="1006" height="660" alt="image" src="https://github.com/user-attachments/assets/bf97a468-3cbd-4d5e-a3a8-721546fa17ae" />

<img width="1009" height="658" alt="image" src="https://github.com/user-attachments/assets/1f7d62e2-f765-451b-99b2-2e1581f00ff1" />

- Action: Pass
- Protocol: any
- Source: chọn Single host or alias → điền INTERNAL_LANS
- Destination: any
- (Tuỳ chọn) Log để dễ theo dõi
- Description: Allow INTERNAL_LANS to anywhere
- Save → Apply Changes.

Rule này cho phép toàn bộ traffic từ 3 VLAN đi qua pfSense (Internet, site-to-site, OpenVPN, v.v.).
Nếu đã có rule “LAN net → any” cũ chỉ áp dụng cho 192.168.10.0/24, ta có thể disable hoặc kéo xuống dưới.

3) Thêm Gateway thủ công

<img width="1013" height="660" alt="image" src="https://github.com/user-attachments/assets/50ecdcc5-1849-4a68-be33-b2c1a9c8f3a5" />

System -> Routing -> Gateways -> Add

<img width="1010" height="657" alt="image" src="https://github.com/user-attachments/assets/2dde9bfe-0744-4715-ba4f-627d4c775787" />

<img width="1011" height="696" alt="image" src="https://github.com/user-attachments/assets/4b8e555d-841f-4b3e-8e79-2241a0845640" />

- Interface: LAN2
- Address Family: IPv4
- Name: LAN2_GW
- Gateway: 192.168.10.1
- Description: Gateway to SWL3
-> Save

4) Static Routes cho các VLAN nằm sau SWL3

<img width="1020" height="660" alt="image" src="https://github.com/user-attachments/assets/702a83bf-1dcd-4898-ab56-b8177054b465" />

<img width="1027" height="660" alt="image" src="https://github.com/user-attachments/assets/abf217c1-b4c2-484a-a28b-a6a2822b3b0d" />

System → Routing → Static Routes → Add

<img width="1024" height="658" alt="image" src="https://github.com/user-attachments/assets/3da058a2-b598-4e2f-a38f-ea64d162174a" />

- Destination network: 192.168.20.0
- Gateway: LAN2_GW - 192.168.10.1
- Description: SWL3_GW - 192.168.20.1
-> Save

Tương tự các Route tiếp theo

- Destination network: 192.168.30.0
- Gateway: LAN2_GW - 192.168.10.1
- Description: SWL3_GW - 192.168.30.1
-> Save


- Destination network: 192.168.40.0
- Gateway: LAN2_GW - 192.168.10.1
- Description: SWL3_GW - 192.168.40.1
-> Save

5) (Khuyến nghị) Gộp luôn NAT outbound bằng alias

Sử dụng chính Alias INTERNAL_LANS vừa tạo bên trên cho NAT các VLAN20,30,40

<img width="1006" height="662" alt="image" src="https://github.com/user-attachments/assets/e91be566-1ca5-4e12-80fc-cafe51ac5175" />

<img width="1005" height="662" alt="image" src="https://github.com/user-attachments/assets/d5a7dadb-fdf7-4648-9c84-844677d93bf1" />

<img width="1004" height="620" alt="image" src="https://github.com/user-attachments/assets/b3b6ea11-da17-4197-9d92-4a25711c240d" />

Firewall → NAT → Outbound (Hybrid/Manual)

- Interface: WAN
- Address Family: IPv4
- Protocol: any
- Source / Type: Network
- Source network for the outbound NAT mapping: INTERNAL_LANS
- Destination: Any
- Address: Interface Address
- Description: NAT for INTERNAL_LANS

-> Save -> Apply Changes

Xoá 3 rule NAT cũ (20/30/40) → Save → Apply.
