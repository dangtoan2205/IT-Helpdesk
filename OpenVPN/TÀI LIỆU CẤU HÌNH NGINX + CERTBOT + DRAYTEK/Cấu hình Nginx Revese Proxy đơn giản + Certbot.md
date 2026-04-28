## 1. Nginx site-avalibale

```
server {
    listen 80;
    server_name DOMAIN;
 
    client_max_body_size 100M;
 
    location / {
        proxy_pass http://BACKEND_IP:PORT;
 
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

## 2. Reload kiểm tra lỗi cú pháp

```
sudo nginx -t && sudo systemctl reload nginx
```

## 3. Cấp chứng chỉ cho domain vừa thêm

```
sudo certbot --nginx -d domain.example
 
sudo nginx -s reload
```
