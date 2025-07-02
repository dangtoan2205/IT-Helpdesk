Reset thời hạn dùng thử 60 ngày trên ESXi 6 / 7 để sử dụng mãi mãi
--------

![image](https://github.com/user-attachments/assets/32086070-4ae5-4af5-92d6-a398257dc47f)

Sau khi hết hạn 60 ngày, ESXi sẽ báo lỗi “Your evaluation license for ESXi has expired.” Các máy ảo đang mở sẽ vẫn tiếp tục hoạt động, nhưng nếu bạn tắt nó đi, sẽ không thể mở lại được nữa.

![image](https://github.com/user-attachments/assets/2ce657a3-9ce4-4a2d-b93d-538c1da5a0c0)

**Dưới đây là cách reset thời hạn dùng thử để bạn sử dụng ESXi mãi mãi mà không bị hạn chế tính năng. Cách này có thể áp dụng cho ESXi 6 lẫn ESXi 7.**

**Lưu ý: Chỉ dùng cho mục đích thử nghiệm và nghịch phá. Nếu sử dụng cho mục đích kinh doanh, bạn cần mua bản quyền từ VMWare.**

## 1. Kích hoạt SSH

Truy cập vào mục **Host** ở menu bên trái, chọn **Actions** –> **Services** –> **Enable Secure Shell (SSH)** để cho phép truy cập vào ESXi host thông qua SSH.

![image](https://github.com/user-attachments/assets/468dc72e-96f6-4f2f-88d1-1e9eda5b59e5)

## 2. Reset bản quyền

Truy cập SSH vào ESXi host và thực hiện các lệnh sau

Xóa bản quyền hiện tại (đã hết hạn)

```bash
rm -r /etc/vmware/license.cfg
```

Copy bản quyền dùng thử (có sẵn trên host) vào thay thế

```bash
cp /etc/vmware/.#license.cfg /etc/vmware/license.cfg
```

Khởi động lại dịch vụ ESXi

```bash
/etc/init.d/vpxa restart
```
hoặc

Truy cập vào mục **Host** ở menu bên trái, chọn **Actions** –> **Reboot** để khởi động lại VM nếu trong trường hợp restart bằng cmd không được.

![sleekshot](https://github.com/user-attachments/assets/c9623198-c043-47a1-ba77-45fa0b54a9a2)


Truy cập lại vào ESXi, bản quyền dùng thử đã được reset về lại 60 ngày.

Cấu hình reset tự động
-------------

Cấu hình hệ thống tự động chạy các đoạn script trên sau mỗi 30 ngày, khỏi phải thao tác thủ công nữa.

Tạo file `reset-license.sh` và lưu nó trong `datastore` của bạn.

```bash
cd /vmfs/volumes/datastore1/vi reset-license.sh
```

Bạn cần thay thế **datastore1** với tên **datastore** tương ứng trên host đang dùng.

ESXi không có sẵn **nano** để chỉnh sửa file, nên bắt buộc phải dùng **vi**. Cách sử dụng vi: bấm **i** để bắt đầu chế độ chỉnh sửa, paste vào nội dung bên dưới. Sau đó bấm tiếp `Esc` `:` `w` `q` và `Enter` để lưu lại và thoát ra

```bash
#!/bin/sh
## remove license
echo 'Removing License'
rm -r /etc/vmware/license.cfg
## get a new trial license
echo 'Copying new license'
cp /etc/vmware/.#license.cfg /etc/vmware/license.cfg
## restart services
echo 'Restarting VPXA'
/etc/init.d/vpxa restart
```

Thiết lập quyền thực thi cho file `reset-license.sh` và chạy thử

```bash
chmod +x reset-license.sh
./reset-license.sh
```

Tiếp theo, tạo cron để tự động chạy vào ngày 1 của mỗi tháng

```bash
/bin/echo "* * 1 * * /vmfs/volumes/datastore1/reset-license.sh" >> /var/spool/cron/crontabs/root
kill $(cat /var/run/crond.pid)
crond
```

Do ESXi sẽ tự động reset lại file cron mỗi khi khởi động, nên cần phải edit thêm file `/etc/rc.local.d/local.sh` và thêm vào đoạn sau vào trước dòng `exit 0` ở dưới cùng.

```bash
/bin/kill $(cat /var/run/crond.pid)
/bin/echo "* * 1 * * /vmfs/volumes/datastore1/reset-license.sh" >> /var/spool/cron/crontabs/root
crond
```

Vậy là xong. ESXi sẽ luôn luôn hoạt động trong chế độ dùng thử, với đầy đủ tính năng của bản Enterprise để bạn thoải mái nghiên cứu, nghịch phá.

[Nhấn vào đây để xem tài liệu tham khảo](https://calvin.me/reset-esxi-evaluation-license/)
