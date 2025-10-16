1) BẬT L3 ROUTING TRÊN SWITCH
---
Hiện cấu hình chưa có `ip routing`, nên SWL3 chưa route được giữa các VLAN và ra pfSense.

```bash
conf t
hostname SWL3
no ip domain-lookup
ip routing
end
wr
```

2) Tạo các vlan tương ứng

```bash
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

3) Gán IP cho các VLAN interface (SVI)

```bash
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
```

4) Cấu hình các cổng kết nối

```bash
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
```

5) Cấu hình cổng kết nối đến pfSense (e0/0)

```bash
interface e0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
 no shut
```

6) Cấu hình Default route cho SWL3

```bash
conf t
ip route 0.0.0.0 0.0.0.0 192.168.10.254
end
wr
```

7) Cấu hình DHCP server trên SWL3

```bash
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
