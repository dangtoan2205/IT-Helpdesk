Cấu hình phần mềm quản lý thiết bị 
---------
## Bước 1: Cài đặt các gói cần thiết
Trước tiên, hãy cài đặt các phần mềm cần thiết cho máy chủ của bạn:

1. **Cập nhật hệ thống:**

```
sudo apt update && sudo apt upgrade -y
```

2. Cài đặt các gói phần mềm cần thiết:

- Nginx: Máy chủ web.
- MySQL: Cơ sở dữ liệu.
- PHP: Ngôn ngữ lập trình PHP với các tiện ích cần thiết.
Cài đặt các gói này:

```
sudo apt install nginx mysql-server php-fpm php-mysql php-cli php-xml php-mbstring php-curl php-zip php-bcmath git unzip curl -y
```
## Bước 2: Cấu hình MySQL
1. **Tạo cơ sở dữ liệu và người dùng cho Snipe-IT:**
```
sudo mysql -u root -p
```
Sau đó nhập mật khẩu của MySQL root và thực hiện các câu lệnh sau trong MySQL shell:
```
CREATE DATABASE snipeit;
CREATE USER 'snipeituser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON snipeit.* TO 'snipeituser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
## Bước 3: Clone dự án Snipe-IT từ GitHub
1. **Chuyển đến thư mục web root của Nginx (hoặc tạo thư mục mới nếu cần):**
```
cd /var/www
sudo git clone https://github.com/snipe/snipe-it.git snipeit
```

2. **Cấp quyền cho thư mục:**
```
sudo chown -R www-data:www-data /var/www/snipeit
sudo chmod -R 755 /var/www/snipeit
```
## Bước 4: Cấu hình PHP
1. **Mở và chỉnh sửa file PHP `php.ini` để tối ưu hóa cho Snipe-IT:**

```
sudo nano /etc/php/7.4/fpm/php.ini
```

Thực hiện các thay đổi sau:

- Tìm và chỉnh sửa `date.timezone`:
```
date.timezone = "Asia/Ho_Chi_Minh"
```
Tăng giới hạn bộ nhớ PHP nếu cần (tùy chọn thêm):
```
memory_limit = 256M
```
2. **Khởi động lại PHP-FPM:**
```
sudo systemctl restart php7.4-fpm
```
## Bước 5: Cấu hình Nginx
1. **Tạo file cấu hình Nginx cho Snipe-IT:** Tạo file cấu hình Nginx cho Snipe-IT trong thư mục `/etc/nginx/sites-available/`:
```
sudo nano /etc/nginx/sites-available/snipeit
```

Thêm nội dung sau vào file:
```
server {
    listen 80;
    server_name seta-snipeit.com;

    root /var/www/snipeit/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
2. **Kích hoạt site và kiểm tra cấu hình:**

```
sudo ln -s /etc/nginx/sites-available/snipeit /etc/nginx/sites-enabled/
sudo nginx -t
```

3. **Khởi động lại Nginx:**

```
sudo systemctl restart nginx
```
## Bước 6: Cấu hình tên miền
1. **Chỉnh sửa DNS để trỏ tên miền tới địa chỉ IP của máy chủ:**

- Truy cập trang quản lý DNS của nhà cung cấp tên miền bạn đang sử dụng.
- Tạo một bản ghi **A record** trỏ tên miền `seta-snipeit.com` đến địa chỉ IP của máy chủ Ubuntu.
2. **Kiểm tra tên miền:** Sau khi DNS được cập nhật (có thể mất từ vài phút đến vài giờ), bạn có thể kiểm tra bằng cách truy cập `http://seta-snipeit.com` từ trình duyệt.
## Bước 7: Cài đặt ứng dụng Snipe-IT
1. **Di chuyển vào thư mục Snipe-IT:**

```
cd /var/www/snipeit
```

2. **Cài đặt các phụ thuộc:**

```
sudo composer install --no-dev --optimize-autoloader
```

3. **Tạo file cấu hình** `.env`:

```
sudo cp .env.example .env
```

4. **Cấu hình kết nối cơ sở dữ liệu: Mở file `.env` để chỉnh sửa thông tin kết nối MySQL:**

```
sudo nano .env
```

Thay đổi các dòng sau:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=snipeit
DB_USERNAME=snipeituser
DB_PASSWORD=password
```

5. **Tạo key cho ứng dụng:**

```
sudo php artisan key:generate
```

6. **Chạy migrations để tạo bảng trong cơ sở dữ liệu:**

```
sudo php artisan migrate --seed
```

## Bước 8: Kiểm tra và hoàn tất
1. **Truy cập vào ứng dụng**: Mở trình duyệt và truy cập http://seta-snipeit.com. Bạn sẽ thấy giao diện đăng nhập của Snipe-IT.

2. **Đăng nhập với tài khoản quản trị mặc định (nếu cần thiết):** Mặc định, bạn có thể đăng nhập với thông tin tài khoản:

- Email: admin@admin.com
- Password: password
3. **Cấu hình thêm SSL (tuỳ chọn):** Nếu bạn muốn bảo mật kết nối với HTTPS, bạn có thể cài đặt Let's Encrypt SSL cho Nginx.
