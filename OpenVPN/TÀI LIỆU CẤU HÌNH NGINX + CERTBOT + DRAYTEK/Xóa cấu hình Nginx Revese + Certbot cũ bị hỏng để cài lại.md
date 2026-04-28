## 1. Xóa config cũ 

```
sudo rm /etc/nginx/sites-enabled/vengy-api.blueoc.group.conf
```

```
sudo rm /etc/nginx/sites-enabled/vengy-api.blueoc.group.conf
```

## 2. Xóa cert

```
sudo certbot delete --cert-name vengy-api.blueoc.group
```

## 3. Reload nginx

```
sudo nginx -t
sudo systemctl reload nginx
```

## 4. Tạo file config mới

```
sudo nano /etc/nginx/sites-available/new-api.blueoc.group.conf
```
 
## 5. Kiểm tra

```
sudo nginx -t
sudo systemctl reload nginx
```
 
## 6. Cấp ssl mới

```
sudo certbot --nginx -d new-api.blueoc.group
```
