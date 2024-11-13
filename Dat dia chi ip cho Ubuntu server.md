Đặt địa chỉ IP cho Ubuntu server

1/ Đặt IP DHCP cho Ubnutu Server khi bắt đầu </br>
2/ Kiểm tra dung lượng ổ cứng </br>
```
df -Th
```

3/ Đổi Password
```
sudo -i
passwd
```

4/ Đặt địa chỉ IP tĩnh cho Ubnutu Server
```
nano /etc/netplan/ --bấm tab--
VD: nano /etc/netplan/50-cloud-init.yaml
```

File cấu hình
```
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp3s0:
       dhcp4: no 
       addresses: [192.168.80.49/23]
       gateway4: 192.168.80.1
       nameservers:
             addresses: [192.168.80.10, 8.8.8.8, 8.8.4.4]

  version: 2
```

5/ Lưu lại cấu hình
```
netplan apply
```
