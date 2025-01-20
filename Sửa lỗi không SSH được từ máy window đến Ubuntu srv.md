Sửa lỗi không thể SSH được
-------------

![image](https://github.com/user-attachments/assets/b54ca173-8862-4161-8db4-bcf184ca447e)

Fix:

- Ubuntu srv

1/ Đảm bảo dịch vụ SSH đang chạy trên Ubuntu:
```
sudo systemctl status ssh
```

Nếu dịch vụ SSH không chạy, khởi động lại:
```
sudo systemctl start ssh
```

Để đảm bảo dịch vụ tự động khởi động sau mỗi lần khởi động máy:
```
sudo systemctl enable ssh
```

2/ Kiểm tra tường lửa (Firewall): Kiểm tra xem tường lửa trên Ubuntu có chặn cổng 22 (SSH) không:
```
sudo ufw status
```

```
sudo ufw allow 22
sudo ufw reload
```


3/ Kiểm tra cấu hình SSH

Tệp cấu hình SSH: Kiểm tra tệp cấu hình /etc/ssh/sshd_config
```
sudo nano /etc/ssh/sshd_config
```

Đảm bảo các dòng sau không bị comment (dấu #):
```
Port 22
PermitRootLogin yes
PasswordAuthentication yes
```

Sau khi chỉnh sửa, khởi động lại dịch vụ SSH:
```
sudo systemctl restart ssh
```

---
- Kiểm tra từ phía Windows
```
ssh-keygen -R 192.168.80.81
```
























