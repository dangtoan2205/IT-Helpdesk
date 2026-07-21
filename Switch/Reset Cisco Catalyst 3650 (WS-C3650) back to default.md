Hướng dẫn reset Swwitch Cisco Catalyst 3650 (WS-C3650) về mặc định khi mất Username/Password
----------

## Bước 1. Tắt nguồn switch
> Rút dây nguồn của switch.

## Bước 2. Vào Boot Loader
> Nhấn và giữ nút MODE.

> Cắm lại nguồn.

> Tiếp tục giữ khoảng 10–15 giây cho đến khi xuất hiện dấu nhắc:

```
switch:
```

Sau đó thả nút **MODE**.

## Bước 3. Khởi tạo Flash

```
flash_init
```

## Bước 4. Kiểm tra hệ thống file
```
dir flash:
```

Đảm bảo thấy các file như:
```
packages.conf
nvram_config
vlan.dat
```

## Bước 5. Bỏ qua Startup Configuration

Kiểm tra biến môi trường:
```
set
```
Đặt biến bỏ qua cấu hình khởi động:
```
set SWITCH_IGNORE_STARTUP_CFG 1
```
Kiểm tra lại:
```
set
```
Nếu thấy:
```
SWITCH_IGNORE_STARTUP_CFG=1
```
thì chuyển sang bước tiếp theo.

## Bước 6. Boot vào IOS
```
boot flash:packages.conf
```
Đợi switch khởi động hoàn tất.

## Bước 7. Vào Privileged EXEC Mode
```
enable
```

## Bước 8. Xóa Startup Configuration
```
erase startup-config
```
Nhấn **Enter** để xác nhận.

## Bước 9. Xóa cơ sở dữ liệu VLAN
```
delete flash:vlan.dat
```
Nhấn **Enter** hai lần để xác nhận xóa.

## Bước 10. Khởi động lại switch
```
reload
```
Khi được hỏi:
```
System configuration has been modified. Save? [yes/no]:
```
Nhập:
```
no
```
Sau đó khi hỏi:
```
Proceed with reload? [confirm]
```
Nhấn **Enter**.

## Bước 11. Hoàn tất

Sau khi switch khởi động lại:

- Không còn yêu cầu Username/Password.
- Tên switch trở về mặc định là Switch.
- Toàn bộ cấu hình và VLAN cũ đã được xóa.
- Kiểm tra sau khi reset
```
show startup-config
show running-config
show vlan brief
```
Nếu `show startup-config` trả về thông báo tương tự:
```
startup-config is not present
```
thì quá trình reset đã thành công.
