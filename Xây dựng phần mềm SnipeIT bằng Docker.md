Xây dựng phần mềm SnipeIT bằng Docker
------------

## Step 1: Pull docker image

```
docker pull snipe/snipe-it
```

-----------

## Step 2: Create file docker-compose.yml

```
docker-compose.yml
```

```
# Compose file for production.

volumes:
  db_data:
  storage:

services:
  app:
    image: snipe/snipe-it:${APP_VERSION:-v7.0.11}
    container_name: seta
    restart: unless-stopped
    volumes:
      - storage:/var/lib/snipeit
    ports:
      - 8888:80
    depends_on:
      db:
        condition: service_healthy
        restart: true
    env_file:
      - snipeenv.docker

  db:
    image: mariadb:11.5.2
    container_name: databases
    restart: unless-stopped
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_DATABASE: snipeit
      MYSQL_USER: snipeit
      MYSQL_PASSWORD: Adminlocal123a@
      MYSQL_ROOT_PASSWORD: Adminlocal123a@
    healthcheck:
      # https://mariadb.com/kb/en/using-healthcheck-sh/#compose-file-example
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 1s
      retries: 5
```

------------

## Step 3: Create file snipeenv.docker

```
snipeenv.docker
```

```
# --------------------------------------------
# REQUIRED: DOCKER SPECIFIC SETTINGS
# --------------------------------------------
APP_VERSION=v6.4.1
APP_PORT=8888

# --------------------------------------------
# REQUIRED: BASIC APP SETTINGS
# --------------------------------------------
APP_ENV=production
APP_DEBUG=false
# Please regenerate the APP_KEY value by calling `docker compose run --rm app php artisan key:generate --show`. Copy paste the value here
APP_KEY=base64:3ilviXqB9u6DX1NRcyWGJ+sjySF+H18CPDGb3+IVwMQ=
APP_URL=http://192.168.81.31:8888
# https://en.wikipedia.org/wiki/List_of_tz_database_time_zones - TZ identifier
APP_TIMEZONE='UTC'
APP_LOCALE=en-US
MAX_RESULTS=500

# --------------------------------------------
# REQUIRED: UPLOADED FILE STORAGE SETTINGS
# --------------------------------------------
PRIVATE_FILESYSTEM_DISK=local
PUBLIC_FILESYSTEM_DISK=local_public

# --------------------------------------------
# REQUIRED: DATABASE SETTINGS
# --------------------------------------------
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT='3306'
DB_DATABASE=snipeit
DB_USERNAME=snipeit
DB_PASSWORD=Adminlocal123a@
MYSQL_ROOT_PASSWORD=Adminlocal123a@
DB_PREFIX=null
DB_DUMP_PATH='/usr/bin'
DB_CHARSET=utf8mb4
DB_COLLATION=utf8mb4_unicode_ci

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
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_TLS_VERIFY_PEER=true
MAIL_FROM_ADDR=you@example.com
MAIL_FROM_NAME='Snipe-IT'
MAIL_REPLYTO_ADDR=you@example.com
MAIL_REPLYTO_NAME='Snipe-IT'
MAIL_AUTO_EMBED_METHOD='attachment'

# --------------------------------------------
# REQUIRED: DATA PROTECTION
# --------------------------------------------
ALLOW_BACKUP_DELETE=false
ALLOW_DATA_PURGE=false

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

# --------------------------------------------
# OPTIONAL: SESSION SETTINGS
# --------------------------------------------
SESSION_LIFETIME=12000
EXPIRE_ON_CLOSE=false
ENCRYPT=false
COOKIE_NAME=snipeit_session
COOKIE_DOMAIN=null
SECURE_COOKIES=false
API_TOKEN_EXPIRATION_YEARS=40

# --------------------------------------------
# OPTIONAL: SECURITY HEADER SETTINGS
# --------------------------------------------
#APP_TRUSTED_PROXIES=192.168.1.1,10.0.0.1,172.16.0.0/12
ALLOW_IFRAMING=false
REFERRER_POLICY=same-origin
ENABLE_CSP=false
CORS_ALLOWED_ORIGINS=null
ENABLE_HSTS=false

# --------------------------------------------
# OPTIONAL: CACHE SETTINGS
# --------------------------------------------
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync
CACHE_PREFIX=snipeit

# --------------------------------------------
# OPTIONAL: REDIS SETTINGS
# --------------------------------------------
REDIS_HOST=null
REDIS_PASSWORD=null
REDIS_PORT=6379

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
RESET_PASSWORD_LINK_EXPIRES=900

# --------------------------------------------
# OPTIONAL: MISC
# --------------------------------------------
LOG_CHANNEL=stderr
LOG_MAX_DAYS=10
APP_LOCKED=false
APP_CIPHER=AES-256-CBC
APP_FORCE_TLS=false
GOOGLE_MAPS_API=
LDAP_MEM_LIM=500M
LDAP_TIME_LIM=600
```


------------

## Step 4: Run

```
docker-compose up -d
```








-----------------------


```
Dockerfile
```

```
FROM ubuntu:24.04
LABEL maintainer="Brady Wetherington <bwetherington@grokability.com>"

# No need to add `apt-get clean` here, reference:
# - https://github.com/snipe/snipe-it/pull/9201
# - https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#apt-get

RUN export DEBIAN_FRONTEND=noninteractive; \
    export DEBCONF_NONINTERACTIVE_SEEN=true; \
    echo 'tzdata tzdata/Areas select Etc' | debconf-set-selections; \
    echo 'tzdata tzdata/Zones/Etc select UTC' | debconf-set-selections; \
    apt-get update -qqy \
 && apt-get install -qqy --no-install-recommends \
apt-utils \
apache2 \
apache2-bin \
libapache2-mod-php8.3 \
php8.3-curl \
php8.3-ldap \
php8.3-mysql \
php8.3-gd \
php8.3-xml \
php8.3-mbstring \
php8.3-zip \
php8.3-bcmath \
php8.3-redis \
php-memcached \
patch \
curl \
wget  \
vim \
git \
cron \
mysql-client \
supervisor \
cron \
gcc \
make \
autoconf \
libc-dev \
libldap-common \
pkg-config \
php8.3-dev \
ca-certificates \
unzip \
dnsutils \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


RUN curl -L -O https://github.com/pear/pearweb_phars/raw/master/go-pear.phar
RUN php go-pear.phar

RUN phpenmod gd
RUN phpenmod bcmath

RUN sed -i 's/variables_order = .*/variables_order = "EGPCS"/' /etc/php/8.3/apache2/php.ini
RUN sed -i 's/variables_order = .*/variables_order = "EGPCS"/' /etc/php/8.3/cli/php.ini

RUN useradd -m --uid 10000 --gid 50 docker

RUN echo export APACHE_RUN_USER=docker >> /etc/apache2/envvars
RUN echo export APACHE_RUN_GROUP=staff >> /etc/apache2/envvars

COPY docker/000-default.conf /etc/apache2/sites-enabled/000-default.conf

#SSL
RUN mkdir -p /var/lib/snipeit/ssl
#COPY docker/001-default-ssl.conf /etc/apache2/sites-enabled/001-default-ssl.conf
COPY docker/001-default-ssl.conf /etc/apache2/sites-available/001-default-ssl.conf

RUN a2enmod ssl
RUN a2ensite 001-default-ssl.conf

COPY . /var/www/html

RUN a2enmod rewrite

COPY docker/column-statistics.cnf /etc/mysql/conf.d/column-statistics.cnf

############ INITIAL APPLICATION SETUP #####################

WORKDIR /var/www/html

#Append to bootstrap file (less brittle than 'patch')
# RUN sed -i 's/return $app;/$env="production";\nreturn $app;/' bootstrap/start.php

#copy all configuration files
# COPY docker/*.php /var/www/html/app/config/production/
COPY docker/docker.env /var/www/html/.env

RUN chown -R docker /var/www/html

RUN \
        rm -r "/var/www/html/storage/private_uploads" && ln -fs "/var/lib/snipeit/data/private_uploads" "/var/www/html/storage/private_uploads" \
      && rm -rf "/var/www/html/public/uploads" && ln -fs "/var/lib/snipeit/data/uploads" "/var/www/html/public/uploads" \
      && rm -r "/var/www/html/storage/app/backups" && ln -fs "/var/lib/snipeit/dumps" "/var/www/html/storage/app/backups" \
      && mkdir -p "/var/lib/snipeit/keys" && ln -fs "/var/lib/snipeit/keys/oauth-private.key" "/var/www/html/storage/oauth-private.key" \
      && ln -fs "/var/lib/snipeit/keys/oauth-public.key" "/var/www/html/storage/oauth-public.key" \
      && ln -fs "/var/lib/snipeit/keys/ldap_client_tls.cert" "/var/www/html/storage/ldap_client_tls.cert" \
      && ln -fs "/var/lib/snipeit/keys/ldap_client_tls.key" "/var/www/html/storage/ldap_client_tls.key" \
      && chown docker "/var/lib/snipeit/keys/" \
      && chown -Rh docker "/var/www/html/storage/" \
      && chmod +x /var/www/html/artisan \
      && echo "Finished setting up application in /var/www/html"

############## DEPENDENCIES via COMPOSER ###################

#global install of composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Get dependencies
USER docker
RUN composer install --no-dev --working-dir=/var/www/html
USER root

############### APPLICATION INSTALL/INIT #################

#RUN php artisan app:install
# too interactive! Try something else

#COPY docker/app_install.exp /tmp/app_install.exp
#RUN chmod +x /tmp/app_install.exp
#RUN /tmp/app_install.exp

############### DATA VOLUME #################

VOLUME ["/var/lib/snipeit"]

##### START SERVER

COPY docker/startup.sh docker/supervisord.conf /
COPY docker/supervisor-exit-event-listener /usr/bin/supervisor-exit-event-listener
RUN chmod +x /startup.sh /usr/bin/supervisor-exit-event-listener

CMD ["/startup.sh"]

EXPOSE 80
EXPOSE 443
```
