Cấu hình phần mềm quản lý thiết bị 
---------
## Step 1: Update System

```
sudo apt update
sudo apt upgrade
```

## Step 2: Install Required Dependencies
```
sudo add-apt-repository ppa:ondrej/php
```
```
sudo apt update
```
```
sudo apt install php8.1 -y
```
```
sudo apt-get install -y php8.1-cli php8.1-common php8.1-mysql php8.1-zip php8.1-gd php8.1-mbstring php8.1-curl php8.1-xml php8.1-bcmath php8.1-redis php8.1-fpm php8.1-intl php8.1-zip php8.1-soap php8.1-opcache
```
```
sudo apt install apache2 mysql-server php php-cli php-mysql php-mbstring php-xml php-curl php-zip php-gd composer git unzip
```

phpmyadmin upload size incress conf  
===

```
sudo nano /etc/php/8.1/apache2/php.ini
```

Edit
```
max_execution_time = -1
max_input_time = -1
memory_limit = -1
post_max_size = 3000M
upload_max_filesize = 2048M
```

```
sudo nano /etc/apache2/mods-enabled/dir.conf
```

<Đặt index.php lên đầu tiên và xóa index.php đoạn gần cuối đi> </b>
Replace index.php  , index.html

```
sudo systemctl restart php8.1-fpm.service
```

```
sudo phpenmod mbstring
sudo a2enmod rewrite
sudo systemctl restart apache2
```

conf

```
sudo nano /etc/apache2/apache2.conf
```
None -> All

![image](https://github.com/user-attachments/assets/db23a42a-4c9b-45c3-a86a-ccee26c98f51)

![image](https://github.com/user-attachments/assets/7f4111a4-d1db-46ea-b819-fc9e50ba2db6)


## Step 3: Configure MySQL
Install SQL
```
sudo apt update
sudo apt install mysql-server
```

```
sudo systemctl start mysql
sudo systemctl enable mysql
```

```
sudo mysql_secure_installation
```
![image](https://github.com/user-attachments/assets/df94a06b-0938-452d-9c73-6dd0705641d9)

![image](https://github.com/user-attachments/assets/55423b8e-e188-4767-9d5a-79e51ac10ec1)

![image](https://github.com/user-attachments/assets/27076d55-333e-4430-a5d7-c8e4efc3803f)

![image](https://github.com/user-attachments/assets/44d574b5-9486-48fe-be79-c5cb28dd3e6b)

![image](https://github.com/user-attachments/assets/b8ef832d-dd9e-48fb-ba1f-20e2fe937650)

![image](https://github.com/user-attachments/assets/00f452d1-ce93-4416-8161-05bfcca759fa)

```
sudo mysql
```

```
CREATE USER 'snipeit'@'localhost' IDENTIFIED BY 'Adminlocal123a@';
```
```
GRANT ALL PRIVILEGES ON snipeit_db.* TO 'snipeit'@'localhost';
```
```
FLUSH PRIVILEGES;
```
```
CREATE DATABASE snipeit_db;
```
```
exit;
```


## Step 4: Download Snipe-IT
```
sudo git clone https://github.com/snipe/snipe-it snipeit
```

==== (Khong can thiet) symlink  create ====
```
pwd
```
```
sudo ln -sf /home/ubuntu/snipeit   /var/www/html
```

```
cd /home/ubuntu/snipeit
```

## Step 5: Install Dependencies via Composer

```
sudo composer install --no-dev --prefer-source
```

Bấm -> yes </br>
Nếu bị lỗi này, fix như sau:
![image](https://github.com/user-attachments/assets/d8f704c2-b012-4663-a227-5fa9063f8572)

```
php -v
```
```
sudo update-alternatives --config php
```
Bấm -> 2 </br>
![image](https://github.com/user-attachments/assets/578700f7-0a9b-4308-8fe2-63b2254fb863)

```
sudo composer install --no-dev --prefer-source
```

## Step 6: Configure Environment Variables   === 
```
sudo cp .env.example .env
```
```
sudo nano .env
```
```
APP_ENV=local
APP_DEBUG=false
APP_URL=http://192.168.81.156

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=snipeit_db
DB_USERNAME=snipeit
DB_PASSWORD=Adminlocal123a@
```

```
sudo php artisan optimize:clear
```

## Step 7: Set Permissions

```
sudo chmod -R 755 /home/ubuntu
```
```
sudo chown -R www-data:www-data storage public/uploads
```
```
sudo chmod -R 755 storage public/uploads
```
```
sudo chown -R www-data:www-data snipeit
```

## Step 8: Generate Application Key  === 
```
sudo php artisan key:generate
```


## Step 9: Configure Apache  ====
```
sudo nano /etc/apache2/sites-available/snipeit.conf
```
```
<VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/snipeit/public
    ServerName your_domain_or_ip

    <Directory /var/www/html/snipeit/public>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

```
sudo a2ensite snipeit.conf
```
```
sudo systemctl reload apache2
```
```
sudo a2enmod rewrite
```
```
sudo systemctl restart apache2
```
## Step 11: Set Up Cron Jobs  
```
cd /var/www/html/
```
```
ls
```
```
sudo chmod -R 755 snipeit
```
```
sudo chown -R www-data:www-data snipeit
```
```
sudo service apache2 restart
```


-----
Cấu hình chuyển Apache2 thành port 81, Nginx thành port 80
---
## Apache 

- Mở file cấu hình của Apache (000-default.conf hoặc file cấu hình tương ứng trong /etc/apache2/sites-available/):
```
sudo nano /etc/apache2/ports.conf
```
![image](https://github.com/user-attachments/assets/20b3c81c-9e3e-45d7-88ba-60886ac03259)

```
/etc/apache2/sites-available/snipeit.conf
```
![image](https://github.com/user-attachments/assets/bf61264e-e9df-4d23-9399-a1c0854b048e)

- Thêm hoặc sửa dòng sau để Apache lắng nghe trên cổng 81:
```
Listen 81
```
![image](https://github.com/user-attachments/assets/6e0a1b41-71bd-4a12-9b94-56853488ec95)

- Sửa phần VirtualHost để sử dụng cổng 81:

![image](https://github.com/user-attachments/assets/2d9e24ef-d4fe-451a-9673-975618bf27f9)

- Sau khi chỉnh sửa xong, bạn cần reload hoặc restart lại Apache để áp dụng cấu hình mới:
```
sudo systemctl restart apache2
```

- Kiểm tra Apache đang chạy trên cổng 81:
```
sudo ss -tuln | grep :81
```

- Cấu hình trong .env
```
 nano /var/www/html/snipe-it/.env
```
![image](https://github.com/user-attachments/assets/f29e9389-9ec9-418e-9bea-58afadac42b9)

 
Nginx
--------------------

- /etc/nginx/site-available/snipeit.conf
```
#server {
#       listen 80;
#       listen [::]:80;
#
#       server_name 192.168.81.156;
#
#       root /var/www/html/snipe-it/public;
#       index index.html index.htm;
#
#       location / {
#               try_files $uri $uri/ =404;
#       }
#}

server {
    listen 80;
    listen [::]:80;

    server_name _;

    root /var/www/html/snipe-it/public;
    index index.php index.html index.htm;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;  # Update PHP version if needed
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

- /var/www/html/snipeit
```
# --------------------------------------------
# REQUIRED: BASIC APP SETTINGS
# --------------------------------------------
APP_ENV=local
APP_DEBUG=false
APP_KEY=base64:7PLlcqo2yUxjec2kRFmsPoBq8KFAy3Y67uYqHghGI/Q=
APP_URL=http://192.168.81.156
APP_TIMEZONE='UTC'
APP_LOCALE='en-US'
MAX_RESULTS=500

# --------------------------------------------
# REQUIRED: UPLOADED FILE STORAGE SETTINGS
# --------------------------------------------
PRIVATE_FILESYSTEM_DISK=local
PUBLIC_FILESYSTEM_DISK=local_public

#PRIVATE_FILESYSTEM_DISK=s3_private
#PUBLIC_FILESYSTEM_DISK=s3_public


# --------------------------------------------
# REQUIRED: DATABASE SETTINGS
# --------------------------------------------
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=snipeit_db
DB_USERNAME=snipeit
DB_PASSWORD=Adminlocal123a@
DB_PREFIX=null
DB_DUMP_PATH='/usr/bin'
DB_CHARSET=utf8mb4
DB_COLLATION=utf8mb4_unicode_ci
DB_SANITIZE_BY_DEFAULT=false


# --------------------------------------------
# OPTIONAL: SSL DATABASE SETTINGS
# --------------------------------------------
DB_SSL=false
DB_SSL_IS_PAAS=false
DB_SSL_KEY_PATH=null
DB_SSL_CERT_PATH=null
DB_SSL_CA_PATH=null
DB_SSL_CIPHER=null
DB_SSL_VERIFY_SERVER=null

# --------------------------------------------
# REQUIRED: OUTGOING MAIL SERVER SETTINGS
# --------------------------------------------
MAIL_MAILER=smtp
MAIL_HOST=email-smtp.us-west-2.amazonaws.com
MAIL_PORT=587
MAIL_USERNAME=YOURUSERNAME
MAIL_PASSWORD=YOURPASSWORD
MAIL_FROM_ADDR=you@example.com
MAIL_FROM_NAME='Snipe-IT'
MAIL_REPLYTO_ADDR=you@example.com
MAIL_REPLYTO_NAME='Snipe-IT'
MAIL_AUTO_EMBED_METHOD='attachment'
MAIL_TLS_VERIFY_PEER=true

# MAIL_ENCRYPTION is no longer supported. SymfonyMailer will use tls if it's
# advertised, and won't if it's not. If you want to use your mail server's IP but it's failing
# because of certificate errors, set MAIL_TLS_VERIFY_PEER-true

# --------------------------------------------
# REQUIRED: IMAGE LIBRARY
# This should be gd or imagick
# --------------------------------------------
IMAGE_LIB=gd


# --------------------------------------------
# OPTIONAL: BACKUP SETTINGS
# --------------------------------------------
MAIL_BACKUP_NOTIFICATION_DRIVER=null
MAIL_BACKUP_NOTIFICATION_ADDRESS=null
BACKUP_ENV=true
ALLOW_BACKUP_DELETE=false
ALLOW_DATA_PURGE=true

# --------------------------------------------
# OPTIONAL: SESSION SETTINGS
# --------------------------------------------
SESSION_DRIVER=file
SESSION_LIFETIME=12000
EXPIRE_ON_CLOSE=false
ENCRYPT=false
COOKIE_NAME=snipeit_session
PASSPORT_COOKIE_NAME='snipeit_passport_token'
COOKIE_DOMAIN=null
SECURE_COOKIES=false
API_TOKEN_EXPIRATION_YEARS=15
BS_TABLE_STORAGE=cookieStorage
BS_TABLE_DEEPLINK=true

# --------------------------------------------
# OPTIONAL: SECURITY HEADER SETTINGS
# --------------------------------------------
APP_TRUSTED_PROXIES=192.168.1.1,10.0.0.1
ALLOW_IFRAMING=false
REFERRER_POLICY=same-origin
ENABLE_CSP=false
ADDITIONAL_CSP_URLS=null
CORS_ALLOWED_ORIGINS=null
ENABLE_HSTS=false

# --------------------------------------------
# OPTIONAL: CACHE SETTINGS
# --------------------------------------------
CACHE_DRIVER=file
QUEUE_DRIVER=sync
CACHE_PREFIX=snipeit

# --------------------------------------------
# OPTIONAL: REDIS SETTINGS
# --------------------------------------------
REDIS_HOST=null
REDIS_PASSWORD=null
REDIS_PORT=null

# --------------------------------------------
# OPTIONAL: MEMCACHED SETTINGS
# --------------------------------------------
MEMCACHED_HOST=null
MEMCACHED_PORT=null

# --------------------------------------------
# OPTIONAL: PUBLIC S3 Settings
# --------------------------------------------
PUBLIC_AWS_SECRET_ACCESS_KEY=null
PUBLIC_AWS_ACCESS_KEY_ID=null
PUBLIC_AWS_DEFAULT_REGION=null
PUBLIC_AWS_BUCKET=null
PUBLIC_AWS_URL=null
PUBLIC_AWS_BUCKET_ROOT=null

# --------------------------------------------
# OPTIONAL: PRIVATE S3 Settings
# --------------------------------------------
PRIVATE_AWS_ACCESS_KEY_ID=null
PRIVATE_AWS_SECRET_ACCESS_KEY=null
PRIVATE_AWS_DEFAULT_REGION=null
PRIVATE_AWS_BUCKET=null
PRIVATE_AWS_URL=null
PRIVATE_AWS_BUCKET_ROOT=null

# --------------------------------------------
# OPTIONAL: AWS Settings
# --------------------------------------------
AWS_ACCESS_KEY_ID=null
AWS_SECRET_ACCESS_KEY=null
AWS_DEFAULT_REGION=null

# --------------------------------------------
# OPTIONAL: LOGIN THROTTLING
# --------------------------------------------
LOGIN_MAX_ATTEMPTS=5
LOGIN_LOCKOUT_DURATION=60
LOGIN_AUTOCOMPLETE=false

# --------------------------------------------
# OPTIONAL: FORGOTTEN PASSWORD SETTINGS
# --------------------------------------------
RESET_PASSWORD_LINK_EXPIRES=15
PASSWORD_CONFIRM_TIMEOUT=10800
PASSWORD_RESET_MAX_ATTEMPTS_PER_MIN=50

# --------------------------------------------
# OPTIONAL: MISC
# --------------------------------------------
LOG_CHANNEL=single
LOG_MAX_DAYS=10
APP_LOCKED=false
APP_CIPHER=AES-256-CBC
APP_FORCE_TLS=false
APP_ALLOW_INSECURE_HOSTS=false
GOOGLE_MAPS_API=
LDAP_MEM_LIM=500M
LDAP_TIME_LIM=600
IMPORT_TIME_LIMIT=600
IMPORT_MEMORY_LIMIT=500M
REPORT_TIME_LIMIT=12000
REQUIRE_SAML=false
API_THROTTLE_PER_MINUTE=120
CSV_ESCAPE_FORMULAS=true
LIVEWIRE_URL_PREFIX=null

# --------------------------------------------
# OPTIONAL: HASHING
# --------------------------------------------
HASHING_DRIVER='bcrypt'
BCRYPT_ROUNDS=10
ARGON_MEMORY=1024
ARGON_THREADS=2
ARGON_TIME=2

# --------------------------------------------
# OPTIONAL: SCIM
# --------------------------------------------
SCIM_TRACE=false
SCIM_STANDARDS_COMPLIANCE=false
```










--------------
Để kết nối tới MySQL trên server Ubuntu từ một máy khác (ví dụ như DBeaver), bạn cần thực hiện một số bước như sau:
--------

## 1. Kiểm tra MySQL lắng nghe trên tất cả các địa chỉ IP

- Mở file cấu hình MySQL:
```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
- Tìm dòng sau:
```
bind-address = 127.0.0.1
```
- Sửa thành:
```
bind-address = 0.0.0.0
```
- Khởi động lại dịch vụ MySQL:
```
sudo systemctl restart mysql
```
## 2. Tạo tài khoản mới cho 'snipeit'@'%'
```
sudo mysql -u root -p

CREATE USER 'snipeit'@'%' IDENTIFIED BY 'Adminlocal123a@';

GRANT ALL PRIVILEGES ON snipeit_db.* TO 'snipeit'@'%';

FLUSH PRIVILEGES;
```

Kiểm tra lại
```
SELECT host, user FROM mysql.user;
```

## 3. Mở port MySQL trên tường lửa
```
sudo ufw allow 3306/tcp

sudo ufw status
```

## 4. Kiểm tra kết nối
```
netstat -tlpn | grep 3306
```


---
Kiểm tra kết nối mạng từ máy cục bộ
-----


## Bật Telnet Client trên Windows
- Mặc định, telnet không được bật trên Windows. Bạn cần bật tính năng này:
    - Nhấn `Windows + R`, nhập `optionalfeatures`, và nhấn `Enter`.
    - Trong cửa sổ "Windows Features", cuộn xuống và tìm `Telnet Client`.
    - Tích vào ô `Telnet Client` và nhấn `OK`.
    - Chờ quá trình cài đặt hoàn tất.
```
telnet 192.168.80.81 3306
```

## Kết nối DB của 192.168.80.81 

1/ `Mở DBeaver.`

2/ Trong danh sách kết nối, chọn kết nối MySQL của bạn, nhấp chuột phải và chọn `Edit Connection`.

3/ Chuyển đến tab `Driver Settings.`

4/ Trong phần `URL Options`, thêm dòng sau vào cuối URL:
```
?allowPublicKeyRetrieval=true
```

Ví dụ URL đầy đủ:
```
jdbc:mysql://192.168.80.81:3306/snipeit_db?allowPublicKeyRetrieval=true&useSSL=false
```

















