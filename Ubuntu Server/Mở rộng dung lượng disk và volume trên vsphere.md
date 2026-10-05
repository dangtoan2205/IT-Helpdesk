Mở rộng dung lượng disk và volume trên vsphere
-----

> **Kiểm tra bằng câu lệnh**y
> ```
> lsblk
>
> df -h
> ```

1/ Mở rộng partition sda3 theo disk
```
sudo growpart /dev/sda 3
```

2/ Mở rộng LVM Physical Volume
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

