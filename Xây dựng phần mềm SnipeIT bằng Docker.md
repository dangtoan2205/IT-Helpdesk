Xây dựng phần mềm SnipeIT bằng Docker
------------

## Step 1: Pull docker image

```
docker pull toandn2205/snipeit
```

-----------

## Step 2: Create file docker-compose.yml

```
docker-compose.yml
```

```
version: '3.8'

volumes:
  db_data:
  storage_seta:
  storage_blue:
  storage_ai:

networks:
  snipeit_net:

services:
  db:
    image: mariadb:11.5.2
    container_name: mariadb-databases
    restart: unless-stopped
    networks:
      - snipeit_net
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: Adminlocal123a@
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 1s
      retries: 5

  seta:
    image: snipeit-build
    container_name: snipeit-seta
    restart: unless-stopped
    networks:
      - snipeit_net
    volumes:
      - storage_seta:/var/lib/snipeit
    ports:
      - "6666:80"
    depends_on:
      db:
        condition: service_healthy
    env_file:
      - ./snipeenv_seta.docker

  blue:
    image: snipeit-build
    container_name: snipeit-blue
    restart: unless-stopped
    networks:
      - snipeit_net
    volumes:
      - storage_blue:/var/lib/snipeit
    ports:
      - "7777:80"
    depends_on:
      db:
        condition: service_healthy
    env_file:
      - ./snipeenv_blue.docker

  ai:
    image: snipeit-build
    container_name: snipeit-ai
    restart: unless-stopped
    networks:
      - snipeit_net
    volumes:
      - storage_ai:/var/lib/snipeit
    ports:
      - "8888:80"
    depends_on:
      db:
        condition: service_healthy
    env_file:
      - ./snipeenv_ai.docker

```

------------

## Step 3: Create file snipeenv_seta.docker

```
snipeenv_seta.docker
```

```
APP_VERSION=v6.4.1
APP_PORT=6666
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:3ilviXqB9u6DX1NRcyWGJ+sjySF+H18CPDGb3+IVwMQ=
APP_URL=http://192.168.80.81:6666
APP_TIMEZONE='UTC'
APP_LOCALE=en-US
MAX_RESULTS=500

PRIVATE_FILESYSTEM_DISK=local
PUBLIC_FILESYSTEM_DISK=local_public

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT='3306'
DB_DATABASE=seta
DB_USERNAME=seta
DB_PASSWORD=Adminlocal123a@

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

ALLOW_BACKUP_DELETE=false
ALLOW_DATA_PURGE=false
IMAGE_LIB=gd

SESSION_LIFETIME=12000
EXPIRE_ON_CLOSE=false
ENCRYPT=false
COOKIE_NAME=snipeit_session
SECURE_COOKIES=false
API_TOKEN_EXPIRATION_YEARS=40

LOG_CHANNEL=stderr
LOG_MAX_DAYS=10
APP_LOCKED=false
APP_CIPHER=AES-256-CBC
```


## Step 4: Create file snipeenv_blue.docker

```
snipeenv_blue.docker
```

```
APP_VERSION=v6.4.1
APP_PORT=7777
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:3ilviXqB9u6DX1NRcyWGJ+sjySF+H18CPDGb3+IVwMQ=
APP_URL=http://192.168.80.81:7777
APP_TIMEZONE='UTC'
APP_LOCALE=en-US
MAX_RESULTS=500

PRIVATE_FILESYSTEM_DISK=local
PUBLIC_FILESYSTEM_DISK=local_public

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT='3306'
DB_DATABASE=blue
DB_USERNAME=blue
DB_PASSWORD=Adminlocal123a@

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

ALLOW_BACKUP_DELETE=false
ALLOW_DATA_PURGE=false
IMAGE_LIB=gd

SESSION_LIFETIME=12000
EXPIRE_ON_CLOSE=false
ENCRYPT=false
COOKIE_NAME=snipeit_session
SECURE_COOKIES=false
API_TOKEN_EXPIRATION_YEARS=40

LOG_CHANNEL=stderr
LOG_MAX_DAYS=10
APP_LOCKED=false
APP_CIPHER=AES-256-CBC
```

## Step 5: Create file snipeenv_ai.docker

```
snipeenv_ai.docker
```

```
APP_VERSION=v6.4.1
APP_PORT=8888
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:3ilviXqB9u6DX1NRcyWGJ+sjySF+H18CPDGb3+IVwMQ=
APP_URL=http://192.168.80.81:8888
APP_TIMEZONE='UTC'
APP_LOCALE=en-US
MAX_RESULTS=500

PRIVATE_FILESYSTEM_DISK=local
PUBLIC_FILESYSTEM_DISK=local_public

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT='3306'
DB_DATABASE=ai
DB_USERNAME=ai
DB_PASSWORD=Adminlocal123a@

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

ALLOW_BACKUP_DELETE=false
ALLOW_DATA_PURGE=false
IMAGE_LIB=gd

SESSION_LIFETIME=12000
EXPIRE_ON_CLOSE=false
ENCRYPT=false
COOKIE_NAME=snipeit_session
SECURE_COOKIES=false
API_TOKEN_EXPIRATION_YEARS=40

LOG_CHANNEL=stderr
LOG_MAX_DAYS=10
APP_LOCKED=false
APP_CIPHER=AES-256-CBC
```


------------

## Step 6: Run

```
docker-compose up -d
```









-----------------------


```
Dockerfile
```

```
FROM ubuntu:22.04
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
libapache2-mod-php8.1 \
php8.1-curl \
php8.1-ldap \
php8.1-mysql \
php8.1-gd \
php8.1-xml \
php8.1-mbstring \
php8.1-zip \
php8.1-bcmath \
php8.1-redis \
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
libmcrypt-dev \
php8.1-dev \
ca-certificates \
unzip \
dnsutils \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


RUN curl -L -O https://github.com/pear/pearweb_phars/raw/master/go-pear.phar
RUN php go-pear.phar

RUN pecl install mcrypt

RUN bash -c "echo extension=/usr/lib/php/20210902/mcrypt.so > /etc/php/8.1/mods-available/mcrypt.ini"

RUN phpenmod mcrypt
RUN phpenmod gd
RUN phpenmod bcmath

RUN sed -i 's/variables_order = .*/variables_order = "EGPCS"/' /etc/php/8.1/apache2/php.ini
RUN sed -i 's/variables_order = .*/variables_order = "EGPCS"/' /etc/php/8.1/cli/php.ini

RUN useradd -m --uid 1000 --gid 50 docker

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
