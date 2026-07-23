Lưu ý: Nếu dự định quản lý tập trung bằng Windows Server, DHCP nên được cấp bởi Windows Server, còn pfSense chỉ làm Gateway, NAT và Firewall.
----

Kiến trúc

```
                        INTERNET
                            │
                        ISP VNPT
                            │
                    pfSense Firewall
                    192.168.90.254
                            │
                      Cisco Catalyst
                            │
          -------------------------------------
          │                                   │
      Windows Server                     Client PC
      DC01                               DHCP Client
      192.168.90.10                      192.168.90.50-200
      AD DS
      DNS
      DHCP

```
