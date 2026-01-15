TÀI LIỆU CẤU HÌNH NGINX + CERTBOT + DRAYTEK  
Domain: mcc5b.seta-international.vn
---

## Mục tiêu

- Public domain ra Internet bằng **HTTPS (Let’s Encrypt)**
- Sử dụng **Nginx làm reverse proxy**
- Backend chạy **nội bộ port 7900**
- Router sử dụng **DrayTek**

---

## Bước 0: Chuẩn bị domain

- Domain đã được đăng ký (ví dụ: `mcc5b.seta-international.vn`)
- DNS **trỏ về IP WAN thật của router DrayTek**
- Cloudflare để **DNS only (KHÔNG bật proxy)**

Kiểm tra DNS:

```bash
dig +short mcc5b.seta-international.vn
```


# Bước 1: Mở port 80 và 443 cho server VPN trên DrayTek
## 1.1. Kiểm tra và giải phóng port 80 / 443 trên DrayTek
Đăng nhập router DrayTek → vào:
```bash
System Maintenance → Management
```
Tại tab **IPv4 Management Setup**, đổi port quản trị để giải phóng 80 / 443:

- HTTP Port: `80` → `8080`
- HTTPS Port: `443` → `4433`

→ Lưu cấu hình.
>⚠️ **Quan trọng**
>
> Nếu DrayTek đang bật SSL VPN, cần vào:
>
> ```bash
> VPN and Remote Access → SSL VPN → General Setup
> ```
> và đổi **SSL VPN Port** từ `443` → `1443` hoặc `4443`.

## 1.2. NAT mở port cho server VPN (Nginx)
Vào:
```bash
NAT → Open Ports
```
Chọn cấu hình cho server VPN (server cài Nginx).

Thông tin chung:
- Comment: `Open port 1194 / 80 / 443`
- WAN Interface: `WAN1` (hoặc WAN đang sử dụng)
- Source IP: `Any`
- Private IP: `192.168.60.132` (IP server VPN / Nginx)

Bảng cấu hình port:
| Protocol | Start Port | End Port |
|----------|------------|----------|
| UDP      | 1194       | 1194     |
| TCP      | 80         | 80       |
| TCP      | 443        | 443      |

# Bước 2: Cài đặt và cấu hình Nginx
## 2.1. Cài đặt Nginx
```bash
sudo apt update
sudo apt install nginx -y
```
## 2.2. Cấu hình Nginx (trước khi cấp SSL)

Tạo file cấu hình site:
```bash
sudo nano /etc/nginx/sites-enabled/mcc5b.seta-international.vn.conf
```
Nội dung:
```bash
server {
    listen 80;
    server_name mcc5b.seta-international.vn;

    root /var/www/html;
    client_max_body_size 100M;

    location / {
        proxy_pass http://192.168.60.42:7900;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Kiểm tra và reload Nginx:
```bash
sudo nginx -t && sudo systemctl reload nginx
```

## Bước 2.3: Cấp chứng chỉ SSL bằng Certbot
**Cài đặt Certbot**
```bash
sudo apt install certbot python3-certbot-nginx -y
```

**Chạy Certbot**
```bash
sudo certbot --nginx -d mcc5b.seta-international.vn
```

**Certbot sẽ tự động:**
- Verify domain (HTTP-01)
- Tạo chứng chỉ SSL (Let’s Encrypt)
- Thêm HTTPS server block
- Redirect HTTP (80) → HTTPS (443)

# Bước 3: Xác minh cấu hình và kiểm tra kết nối
## 3.1. Cấu hình Nginx sau khi có SSL (bản chuẩn tham khảo)
```bash
server {

    root /var/www/html;
    server_name mcc5b.seta-international.vn;
    client_max_body_size 100M;

    location / {
        proxy_pass http://192.168.60.42:7900;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/mcc5b.seta-international.vn/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/mcc5b.seta-international.vn/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

server {
    if ($host = mcc5b.seta-international.vn) {
        return 301 https://$host$request_uri;
    }

    server_name mcc5b.seta-international.vn;
    listen 80;
    return 404;
}
```

## 3.2. Kiểm tra truy cập
Mở trình duyệt:
```bash
https://mcc5b.seta-international.vn
```
Nếu ứng dụng trả về đúng nội dung (ví dụ: hiển thị `OK`) → cấu hình thành công.

## 3.3. Kiểm tra chứng chỉ SSL
```bash
openssl s_client -connect mcc5b.seta-international.vn:443
```

# Ghi chú vận hành
- ❌ Không public trực tiếp port 7900 ra Internet
- ✅ Certbot tự gia hạn chứng chỉ qua systemd timer
- Có thể nhân bản cấu hình cho domain khác bằng cách:
- Copy `file .conf`
- Đổi `server_name`
- Đổi IP backend nếu cần



