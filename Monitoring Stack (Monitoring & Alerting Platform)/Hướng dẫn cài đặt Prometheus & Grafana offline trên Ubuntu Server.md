# HƯỚNG DẪN TRIỂN KHAI HỆ THỐNG MONITORING OFFLINE

# PROMETHEUS + GRAFANA TRÊN UBUNTU SERVER

---

# 1. MỤC ĐÍCH

Triển khai hệ thống Monitoring Offline phục vụ việc giám sát hạ tầng máy chủ.

Giai đoạn hiện tại triển khai:

* Prometheus Server
* Grafana Dashboard

Giai đoạn tiếp theo sẽ triển khai:

* Node Exporter
* Dashboard Monitoring Linux Server
* Monitoring CPU, RAM, Disk, Network

---

# 2. KIẾN TRÚC HỆ THỐNG

## Giai đoạn 1

Browser
|
v
Grafana (Port 3000)
|
v
Prometheus (Port 9090)

## Giai đoạn 2

Linux Server
(Node Exporter)
|
v
Prometheus
|
v
Grafana

---

# 3. THÔNG TIN TRIỂN KHAI

| Thành phần | Chức năng            | Port |
| ---------- | -------------------- | ---- |
| Prometheus | Thu thập Metrics     | 9090 |
| Grafana    | Dashboard Monitoring | 3000 |

- Hệ điều hành: Ubuntu Server 24.04 LTS
- User triển khai: manager
- Thư mục cài đặt:
  - Prometheus: **/home/manager/prometheus**
  - Grafana: **/home/manager/grafana**

---

# 4. CHUẨN BỊ BỘ CÀI OFFLINE

Do Ubuntu Server không có Internet nên cần tải bộ cài trên máy Windows có Internet trước.

---

## 4.1 Tải Prometheus

Truy cập:

https://prometheus.io/download/

Tải phiên bản:

prometheus-3.12.0.linux-amd64.tar.gz

---

## 4.2 Tải Grafana

Truy cập:

https://grafana.com/grafana/download

Tải phiên bản Linux AMD64 dạng tar.gz.

Ví dụ:

grafana-enterprise_13.0.2_linux_amd64.tar.gz

---

# 5. COPY FILE TỪ WINDOWS SANG UBUNTU

Mở PowerShell tại thư mục chứa file tải về.

## Copy Prometheus

scp .\prometheus-3.12.0.linux-amd64.tar.gz manager@68.68.6.26:/home/manager/

## Copy Grafana

scp .\grafana-enterprise_13.0.2_linux_amd64.tar.gz manager@68.68.6.26:/home/manager/

> **Nhập password user manager khi được yêu cầu.**

---

# 6. KIỂM TRA FILE TRÊN UBUNTU

SSH vào Ubuntu:
```
ssh manager@68.68.6.26
```
Kiểm tra:
```
cd /home/manager
```
```
ls -lh
```
Kết quả mong đợi:
```
prometheus-3.12.0.linux-amd64.tar.gz
```
```
grafana-enterprise_13.0.2_linux_amd64.tar.gz
```
---

# 7. CÀI ĐẶT PROMETHEUS

## 7.1 Giải nén
```
cd /home/manager
```
```
tar -xzf prometheus-3.12.0.linux-amd64.tar.gz
```

Đổi tên:
```
mv prometheus-3.12.0.linux-amd64 prometheus
```
---

## 7.2 Kiểm tra
```
cd prometheus
```
```
ls
```
Kết quả:
```
prometheus
promtool
prometheus.yml
LICENSE
NOTICE
```
---

## 7.3 Tạo thư mục dữ liệu
```
mkdir -p ~/prometheus/data
```
---

## 7.4 Cấu hình Prometheus

Mở file:
```
nano ~/prometheus/prometheus.yml
```
Nội dung:
```
global:
scrape_interval: 15s
evaluation_interval: 15s

scrape_configs:

* job_name: "prometheus"
  static_configs:

  * targets:

    * "localhost:9090"
```
Lưu:
```
Ctrl + O
Enter
Ctrl + X
```
---

## 7.5 Tạo Script Start

File:
```
~/prometheus/start-prometheus.sh
```
Nội dung:
```
#!/bin/bash

PROM_DIR="/home/manager/prometheus"
DATA_DIR="$PROM_DIR/data"
LOG_FILE="$PROM_DIR/prometheus.log"

mkdir -p "$DATA_DIR"

cd "$PROM_DIR"

if ss -lntp | grep -q ":9090"; then
echo "Port 9090 is already running."
exit 0
fi

nohup "$PROM_DIR/prometheus" 
--config.file="$PROM_DIR/prometheus.yml" 
--storage.tsdb.path="$DATA_DIR" 
--web.listen-address="0.0.0.0:9090" \

> "$LOG_FILE" 2>&1 &

sleep 2

if ss -lntp | grep -q ":9090"; then
echo "Prometheus started successfully."
else
echo "Prometheus failed."
fi
```

Cấp quyền:
```
chmod +x ~/prometheus/start-prometheus.sh
```
---

## 7.6 Tạo Script Stop

File:
```
~/prometheus/stop-prometheus.sh
```
Nội dung:
```
#!/bin/bash

pkill -f "/home/manager/prometheus/prometheus"

echo "Prometheus stopped."

Cấp quyền:

chmod +x ~/prometheus/stop-prometheus.sh
```
---

## 7.7 Khởi động Prometheus
```
cd ~/prometheus
```
```
./start-prometheus.sh
```
---

## 7.8 Kiểm tra Port

```
ss -lntp | grep 9090
```
Kết quả mong đợi:
```
0.0.0.0:9090
```
---

## 7.9 Truy cập giao diện
```
http://IP_SERVER:9090
```
Ví dụ:
```
http://68.68.6.26:9090
```
---

# 8. CÀI ĐẶT GRAFANA

## 8.1 Giải nén
```
cd /home/manager
```
```
tar -xzf grafana-enterprise_13.0.2_linux_amd64.tar.gz
```
Đổi tên:
```
mv grafana-v* grafana
```
---

## 8.2 Kiểm tra
```
cd grafana
```
```
ls
```

Kết quả:
```
bin
conf
data
plugins
public
```
---

## 8.3 Tạo Script Start

File:
```
~/grafana/start-grafana.sh
```
Nội dung:
```
#!/bin/bash

GRAFANA_DIR="/home/manager/grafana"
LOG_FILE="$GRAFANA_DIR/grafana.log"

cd "$GRAFANA_DIR"

if ss -lntp | grep -q ":3000"; then
echo "Port 3000 is already running."
exit 0
fi

nohup "$GRAFANA_DIR/bin/grafana" server 
--homepath="$GRAFANA_DIR" \

> "$LOG_FILE" 2>&1 &

sleep 2

if ss -lntp | grep -q ":3000"; then
echo "Grafana started successfully."
else
echo "Grafana failed."
fi
```

Cấp quyền:
```
chmod +x ~/grafana/start-grafana.sh
```
---

## 8.4 Tạo Script Stop

File:
```
~/grafana/stop-grafana.sh
```
Nội dung:
```
#!/bin/bash

pkill -f "/home/manager/grafana/bin/grafana"

echo "Grafana stopped."
```
Cấp quyền:
```
chmod +x ~/grafana/stop-grafana.sh
```
---

## 8.5 Khởi động Grafana
```
cd ~/grafana
```
```
./start-grafana.sh
```
---

## 8.6 Kiểm tra Port
```
ss -lntp | grep 3000
```
Kết quả mong đợi:
```
0.0.0.0:3000
```
---

## 8.7 Truy cập giao diện
```
http://IP_SERVER:3000
```
Ví dụ:
```
http://68.68.6.26:3000
```
---

## 8.8 Tài khoản mặc định

Username:
```
admin
```
Password:
```
admin
```
Đăng nhập lần đầu sẽ yêu cầu đổi mật khẩu.

---

# 9. KẾT NỐI GRAFANA VỚI PROMETHEUS

Đăng nhập Grafana.

- Chọn: Connections → Data Sources → Add Data Source

- Chọn: Prometheus URL: http://localhost:9090

- Chọn: Save & Test

Kết quả mong đợi:
```
Data source is working
```
---

# 10. KIỂM TRA HOẠT ĐỘNG

## Kiểm tra Prometheus
```
ss -lntp | grep 9090
```
## Kiểm tra Grafana
```
ss -lntp | grep 3000
```
## Xem log Prometheus
```
tail -f ~/prometheus/prometheus.log
```
## Xem log Grafana
```
tail -f ~/grafana/grafana.log
```
---

# 11. QUẢN LÝ DỊCH VỤ

## Khởi động Prometheus
```
cd ~/prometheus
```
```
./start-prometheus.sh
```
## Dừng Prometheus
```
cd ~/prometheus
```
```
./stop-prometheus.sh
```

## Khởi động Grafana
```
cd ~/grafana
```
```
./start-grafana.sh
```

## Dừng Grafana
```
cd ~/grafana
```
```
./stop-grafana.sh
```
---

# 12. KẾ HOẠCH TRIỂN KHAI GIAI ĐOẠN 2

Sau khi Prometheus và Grafana hoạt động ổn định sẽ triển khai:

* Node Exporter
* Linux Server Monitoring
* Dashboard Grafana ID 1860
* CPU Monitoring
* RAM Monitoring
* Disk Monitoring
* Network Monitoring

Kiến trúc hoàn chỉnh:

Node Exporter
↓
Prometheus
↓
Grafana

Dashboard mục tiêu:

Node Exporter Full (ID 1860)
