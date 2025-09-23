Hướng dẫn tạo 1 server unifi controller local thay thế cho App Unifi Controller 
----

1/ Thêm mới 1 ubuntu server (set ip static, cài docker)

2/ Tạo thư mục chứa Unifi Controller

```bash
mkdir -p ~/unifi-controller
cd ~/unifi-controller
```

3/ Tạo file `docker-compose.yml`

``bash
nano docker-compose.yml
```

Dán nội dung sau:

```
version: '3'
services:
  unifi:
    image: jacobalberty/unifi:latest
    container_name: unifi-controller
    restart: unless-stopped
    network_mode: host
    volumes:
      - ./data:/unifi/data
      - ./log:/unifi/log
      - ./cert:/unifi/cert
      - ./init:/unifi/init.d
    environment:
      - TZ=Asia/Ho_Chi_Minh
```

4/ Khởi chạy Unifi Controller

```bash
sudo docker-compose up -d
```

Kiểm tra container

```bash
sudo docker ps
```

5/ Truy cập Unifi Controller

Mở trình duyệt web 

```bash
https://<IP-của-server>:8443
```

6/ Tải xuống file backup từ APP UNIFI CONTROLLER

Tại giao diện web chọn "Settings" => "System" => "Backups" => "Download".

7/ Tải file backup này lên server unifi controller mới

Sau khi tải về file backup này ta sẽ tiến hành restore lại các cấu hình hiện tại lên server unifi controller mới.

Tại giao diện web chọn "Settings" => "System" => "Backups" => "Restore".

Upload lên file `.unf`

8/ Tiến hành trỏ lại các AP hiện tại về controller mới

(Đăng nhập bằng tài khoản Super Admin vào giao diện web app unifi controller)

Chọn "Settings" => Nhập tìm kiếm "Device SSH Authentication"

Tại phần Device SSH Authentication (hoặc có thể tự đặt username và password)

Username: dnthauk18

Password: dasdaa...

<img width="1912" height="918" alt="image" src="https://github.com/user-attachments/assets/1c7eb7d0-cd95-4bdc-b6d2-c00b85405613" />

Có thông tin IP của các AP thì tiến hành trỏ từng AP một này về controller mới.

bật "Override inform host" và nhập IP mới:
192.168.1.161

Ví dụ "Nano HD AI 1" có IP là 192.168.1.15

SSH vào AP:

Ví dụ: 

```bash
ssh dnthauk18@192.168.1.15
```

Nhập password

Dán câu lệnh sau để trỏ AP hiện tại về controller mới

```bash
set-inform http://192.168.1.161:8080/inform
```

Kiểm tra các AP này đã online hay chưa







