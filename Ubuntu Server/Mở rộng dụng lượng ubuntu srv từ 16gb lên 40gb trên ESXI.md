## 1. Thêm dung lượng trên ESXI

1/ Truy cập ESXI

2/ Tắt máy ảo ubuntu server đang chạy

3/ Vào Edit settings

4/ Thêm dung lượng cho máy ảo

![image](https://github.com/user-attachments/assets/2c4dfb84-6baf-491f-a131-56e09566c4a9)

-> Save

5/ Khởi động lại ubuntu server

## 2. Mở rộng dung lượng bộ nhớ trên Ubuntu Server

Kiểm tra bằng câu lệnh

```
lsblk
```

```
df -h
```

1/ Mở rộng partition sda3 theo disk
```
sudo growpart /dev/sda 3
```

2/ Mở rộng LVM Phýical Volume
```
sudo pvresive /dev/sda3
```

3/ Gộp toàn bộ dung lượng trống vào Logical Volume
```
sudo lvextend -l +100%FREE -r /dev/mapper/ubuntu--vg-ubuntu--lv
```

4/ Kiểm tra kết quả

```
lsblk
df -h
```

