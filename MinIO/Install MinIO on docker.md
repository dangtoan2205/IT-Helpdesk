## 1/ Dựng lên 1 Ubuntu Server

## 2/ Cài docker lên server

```bash
sudo apt install docker
sudo snap install docker
```

## 3/ Tạo một docker compose để pull file image của MinIO về máy
```bash
mkdir ~/minio
cd minio
nano docker-compose.yml
```

## 4/ Copy nội dung sau vào docker-compose.yml

```bash
version: '3.8'

services:
  minio:
    image: minio/minio:latest
    container_name: minio
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - minio-data:/data
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: admin123
    command: server /data --console-address ":9001"

volumes:
  minio-data:

```

## 5/ Chạy docker

```bash
sudo docker-compose up -d
```

## 6/ Truy cập MinIO
- [Web Console:]( http://localhost:9001)
    - http://localhost:9001
- [API / S3 endpoint:]( http://localhost:9000)
    - http://localhost:9000
 
## 7/ Đăng nhập bằng:
- Username: admin
- Password: admin123









