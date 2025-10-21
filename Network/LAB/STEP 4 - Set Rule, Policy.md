1. Chặn tất cả lưu lượng từ VLAN 20/30/40 → 192.168.1.0/24

2. Tạo 3 OpenVPN server (admin / project / user) và viết rule đúng phạm vi

3. NAT outbound cho các subnet VPN để ra Internet

----

1.Set Alias
----

Vào Firewall → Aliases → IP tạo các alias sau:

- INTERNAL_LANS (Type: Network(s)): (Đây là Alias nhóm các Vlan 20, 30, 40)

<img width="1008" height="656" alt="image" src="https://github.com/user-attachments/assets/14ade843-f2ff-4912-966b-bb4974c06038" />

`192.168.20.0/24`, `192.168.30.0/24`, `192.168.40.0/24`

- CORE_NET (Type: Network): (Đây là Alias cho các IP thuộc dải `192.168.1.0/24`)

<img width="1002" height="659" alt="image" src="https://github.com/user-attachments/assets/57570430-b940-44b2-ba1b-3d44f988ce52" />

`192.168.1.0/24`

- PROJECT_RANGE (Type: Host(s) hoặc Network(s) — pfSense cho phép ghi range): (Đây là Alias dùng để cho phép các IP này sử dụng để làm gì từ `1.50 -> 1.200`)

<img width="1008" height="627" alt="image" src="https://github.com/user-attachments/assets/035abe93-2570-4af9-a4f9-b9cb855399c0" />

`192.168.1.50-192.168.1.200`

- (sẽ dùng sau) VPN_NETS (Type: Network(s)): (Đây là Alias cho các IP của VPN)

<img width="1009" height="653" alt="image" src="https://github.com/user-attachments/assets/d88a6ffa-ab35-416d-9e22-1017da288882" />

`10.8.1.0/24`, `10.8.2.0/24`, `10.8.3.0/24`

Save → Apply Changes.

<img width="1007" height="658" alt="image" src="https://github.com/user-attachments/assets/f36d781d-e240-4edc-b8d9-c32e380560ae" />

2.Chặn VLAN 20/30/40 đi tới 192.168.1.0/24
----

Vào Firewall → Rules → LAN2 (đây là interface 192.168.10.254/24 – tất cả lưu lượng nội bộ đi qua đây):

<img width="1006" height="658" alt="image" src="https://github.com/user-attachments/assets/7900b2e5-1293-4519-a883-62301feff77e" />

<img width="1012" height="657" alt="image" src="https://github.com/user-attachments/assets/d8f09981-982f-430c-aad5-c3bb3d1b508d" />

<img width="1010" height="657" alt="image" src="https://github.com/user-attachments/assets/ce1a969a-76d9-4e73-83f7-25b80c989416" />

<img width="1005" height="656" alt="image" src="https://github.com/user-attachments/assets/10feaf4c-c790-4fb6-a934-048134246470" />

- Add (kéo lên trên các rule “allow” hiện có):
  - Action: Block
  - Interface: LAN
  - Protocol: any
  - Source: `INTERNAL_LANS` (alias)
  - Destination: `CORE_NET` (alias)
  - Description: `Block VLAN20/30/40 -> 192.168.1.0/24`
  - Save

- (Tuỳ chọn, để chắc chắn không ai đi vòng qua firewall): Thêm 1 rule Pass ngay dưới rule Block, cho phép INTERNAL_LANS → any (để vẫn đi Internet).
- Nếu đã có rule Pass bằng alias này rồi thì bỏ qua.

-> Apply Changes.

Lưu ý thứ tự: rule Block phải đứng trên rule Pass.

----

3.Tạo 3 OpenVPN server
----

3.1. Tạo chứng chỉ Certificate Authority (CA) nội bộ trên pfSense để dùng cho OpenVPN.
-----

3.1.1 Tạo CA (Root CA)
------

Vào System → Cert. Manager → Authorities → Add (nút xanh).

<img width="1008" height="650" alt="image" src="https://github.com/user-attachments/assets/228cb9c7-cc19-4a0a-a554-589947e03742" />

<img width="1005" height="634" alt="image" src="https://github.com/user-attachments/assets/bef5b694-f2d5-4c5a-883a-161e80314343" />

<img width="1008" height="660" alt="image" src="https://github.com/user-attachments/assets/76b1d87e-8aae-451e-a7ca-c2ce3cd2975f" />

<img width="1006" height="654" alt="image" src="https://github.com/user-attachments/assets/a8c8e5ac-a0bb-4126-9241-70dcf443a8eb" />

<img width="1007" height="648" alt="image" src="https://github.com/user-attachments/assets/ed208825-c4d7-42f3-a617-635bf69da2f3" />

Điền như sau:

- Descriptive name: ToanVPN-ROOT-CA
- Method: Create an internal Certificate Authority
- Key Type: RSA
  - Key length: 4096 (đủ mạnh; 2048 cũng được nếu máy yếu)
  - Digest Algorithm: SHA256
- Lifetime (days): 3650 (~10 năm)
- Distinguished Name
  - Common Name: ToanVPN-ROOT-CA (rất quan trọng, nên khác tên cert server)
  - Country Code: VN
  - State/Province: Hanoi (hoặc nơi bạn muốn)
  - City: Hanoi
  - Organization: Toan Corp
  - Email Address: (tùy chọn)
- Save

Sau khi Save, sẽ thấy CA xuất hiện trong danh sách “Authorities”.

3.1.2 (Khuyến nghị) Tạo CRL cho CA
-------

Để có thể thu hồi chứng chỉ khi cần:

- System → Cert. Manager → Revocation → Add
- Certificate Authority: chọn ToanVPN-ROOT-CA
- Name: ToanVPN-ROOT-CA-CRL → Save

<img width="1020" height="622" alt="image" src="https://github.com/user-attachments/assets/6305a5c4-fb9f-441c-826c-e2233e7d4764" />

<img width="1007" height="663" alt="image" src="https://github.com/user-attachments/assets/be206812-98c4-4ece-adbd-008f285681fb" />

3.1.3 Tạo Server Certificate(s) dùng CA này
-------

Làm 1 lần cho mỗi OpenVPN server (admin/project/user). Có thể dùng một server cert chung cho cả 3 server, nhưng “ổn” nhất là mỗi server 1 cert.

<img width="1010" height="660" alt="image" src="https://github.com/user-attachments/assets/cbac4a16-7a12-4af0-bbca-9ed8c4606990" />

ToanVPN-ADMIN-SRV-P1194

<img width="1003" height="658" alt="image" src="https://github.com/user-attachments/assets/becb3795-f10f-4be9-8cde-1fc42ef09f94" />

<img width="1010" height="663" alt="image" src="https://github.com/user-attachments/assets/6d0d5c8c-0313-4c2e-a596-fe81bc46eb63" />

<img width="1004" height="620" alt="image" src="https://github.com/user-attachments/assets/20d8011e-1cc0-4765-81cf-1a72f0279fe6" />

ToanVPN-PROJECT-SRV-P1195

<img width="980" height="447" alt="image" src="https://github.com/user-attachments/assets/5a0adde9-ec0a-43b4-8eef-058a6439a090" />

<img width="951" height="602" alt="image" src="https://github.com/user-attachments/assets/e0cbe2f4-be15-4666-bc9c-abff187f5725" />

<img width="962" height="482" alt="image" src="https://github.com/user-attachments/assets/f31ecf78-b895-48dd-b64a-92993ba149f8" />

ToanVPN-USER-SRV-P1196

<img width="965" height="458" alt="image" src="https://github.com/user-attachments/assets/606be52c-8c68-46e6-884c-32e99e4fce8f" />

<img width="953" height="655" alt="image" src="https://github.com/user-attachments/assets/f7a03b53-49c7-4a39-bf55-1b057b601c6e" />

<img width="964" height="479" alt="image" src="https://github.com/user-attachments/assets/e9be7fe9-fd69-41d3-80dd-f2650dc3c775" />

- System → Cert. Manager → Certificates → Add/Sign
- Method: Create an internal certificate
- Descriptive name:
  - ToanVPN-ADMIN-SRV (cho server port 1194)
  - ToanVPN-PROJECT-SRV (1195)
  - ToanVPN-USER-SRV (1196)
- Certificate authority: ToanVPN-ROOT-CA
- Key Type: RSA 4096, Digest: SHA256
- Lifetime: 1825 (5 năm) hoặc 1095 (3 năm)
- Common Name: trùng tên ở bước 3 cho dễ nhớ (ví dụ ToanVPN-ADMIN-SRV)
- Certificate Type: Server Certificate
-> Save

3.1.4 Tạo User Certificate(s) cho người dùng VPN
-------


3.2 Cấu hình Server VPN
------

3.2.1 VPN Admin
-------

<img width="950" height="631" alt="image" src="https://github.com/user-attachments/assets/8d00a1a0-74d5-4ef7-b4cb-9eecb4c3a0d6" />

<img width="941" height="598" alt="image" src="https://github.com/user-attachments/assets/639eef96-a03b-4a04-bcfe-07e05e8249d9" />

<img width="952" height="637" alt="image" src="https://github.com/user-attachments/assets/673af799-68d6-449a-903f-fb64c9930833" />

-> Save

- Description: VPN Server Admin
- Server mode: Peer to Peer (SSL/TLS)
- Device mode: tun
- Protocol: UDP on IPv4 only
- Interface: WAN
- Local port: 1194
- Peer Certificate Authority: ToanVPN-ROOT-CA
- Server certificate: ToanVPN-ADMIN-SRV-P1194
- IPv4 Tunnel Network: 10.8.1.0/24
- 


















Nhớ set Rule allow udp 1194



















