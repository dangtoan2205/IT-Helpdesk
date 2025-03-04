Đặt địa chỉ IP tĩnh cho Ubuntu server
------

1/ Đặt IP DHCP cho Ubnutu Server khi bắt đầu </br>
2/ Kiểm tra dung lượng ổ cứng </br>
```
df -Th
```

3/ Đổi Password
```
sudo -i
```
```
passwd
```

4/ Đặt địa chỉ IP tĩnh cho Ubnutu Server
```
sudo nano /etc/netplan/
```
VD: nano /etc/netplan/50-cloud-init.yaml


File cấu hình
```
network:
  ethernets:
    enp3s0:
       dhcp4: no
       addresses: [192.168.80.49/23]
       routes:
         - to: default
           via: 192.168.80.1
       nameservers:
         addresses: [192.168.80.10, 8.8.8.8, 8.8.4.4]
  version: 2

```

5/ Lưu lại cấu hình
```
sudo netplan apply
```


-----------
Fix
-----------

![image](https://github.com/user-attachments/assets/ca0d36aa-1746-4170-898f-1e4f586f5be8)

```
sudo chmod 600 /etc/netplan/00-installer-config.yaml
```

```
sudo apt update
sudo apt install openvswitch-switch
```

```
sudo systemctl start openvswitch-switch
```

```
sudo systemctl enable openvswitch-switch
```

```
sudo systemctl status openvswitch-switch
```

```
sudo netplan apply
```

------------
Nếu bị lỗi không phân giải được tên miền DNS

```
sudo nano /etc/resolv.conf
```

Thêm dòng sau (nếu chưa có):

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Lưu lại, thử ping:

```
ping google.com
ping 8.8.8.8

```



