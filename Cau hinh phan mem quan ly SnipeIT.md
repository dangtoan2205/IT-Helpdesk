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

## Nginx


























