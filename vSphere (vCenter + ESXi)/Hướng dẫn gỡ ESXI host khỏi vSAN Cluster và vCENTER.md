HƯỚNG DẪN GỠ ESXi HOST KHỎI vSAN CLUSTER VÀ vCENTER
-----

**Môi trường:**
- vCenter: `172.30.50.50`
- Cluster: `vSan-Cluter`
- ESXi cần gỡ: `172.30.50.52`
- ESXi giữ lại: `172.30.50.51`, `.53`, `.54`

## Bước 0. Chuyển ESXI Host thành chế độ Maintenance ModeMode
`Vào VCenter` -> `Chọn vSan Cluster` -> `Chuột phải vào IP (Host ESXI)` -> `Maintenance Mode` -> `Enter Maintenance Mode`

## Bước 1. Kiểm tra vSAN Health

Truy cập **vCenter** → chọn **Cluster**:

`Monitor` → `vSAN` → `Skyline Health` → `RETEST`

Kiểm tra:
- `Cluster Health` không có lỗi nghiêm trọng.
- `Virtual Objects`: Healthy.
- `Resyncing Objects`: 0.
- `Dung lượng vSAN` đủ sau khi loại bỏ host.
- `Storage Policy` vẫn đáp ứng khi chỉ còn 3 host.

## Bước 2. Chạy Data Migration Pre-check

Vào:

`Monitor` → `vSAN` → `Data Migration Pre-check`

1. Chọn host `172.30.50.52`.
2. Chọn **Full data migration**.
3. Chạy `Pre-check`.
4. Kiểm tra `Capacity`, `Object Accessibility` và `Policy Compliance`.

**Chú ý:** Nếu host đã ở **Maintenance Mode**, **Pre-check** có thể không chạy được. Cần thực hiện kiểm tra trước khi đưa host vào Maintenance Mode.

## Bước 3. Đưa ESXi vào Maintenance Mode

1. Chuột phải `172.30.50.52`.
2. Chọn **Maintenance Mode → Enter Maintenance Mode**.
3. Chọn **Full data migration**.
4. Chờ quá trình di chuyển VM và dữ liệu hoàn tất.
5. Kiểm tra task `Enter Maintenance Mode = Completed`.

**Chú ý:**
- `Full data migration` là lựa chọn ưu tiên khi loại bỏ host vĩnh viễn.
- Không chọn `No data migration` nếu chưa xác minh dữ liệu.
- Không tắt nguồn server trong quá trình di chuyển.
- Kiểm tra lại `Resyncing Objects` và `Virtual Objects` trước khi tiếp tục.

## Bước 4. Gỡ ổ đĩa khỏi vSAN

Vào:

`Cluster` → `Configure` → `vSAN` → `Disk Management`

1. Chọn host `172.30.50.52`.
2. Nhấn **VIEW DISKS**.
3. Chọn ổ đĩa thuộc vSAN.
4. Nhấn **REMOVE DISK**.
5. Kiểm tra cảnh báo, xác nhận **REMOVE** nếu dữ liệu đã được di chuyển an toàn.
6. Chờ task Completed.
7. Thực hiện tương tự với ổ còn lại.

**Kết quả:** `0 claimed disks`.

**Chú ý quan trọng:**
- Khi host ở `Maintenance Mode`, thao tác `Remove Disk` có thể không di chuyển thêm dữ liệu.
- Chỉ `Remove` khi đã xác nhận dữ liệu được bảo vệ và không còn object phụ thuộc vào ổ.
- Không xóa hoặc format ổ đĩa thủ công.
- Quy trình `Remove Disk` áp dụng cho `vSAN ESA`; với `vSAN OSA` cần xử lý `Disk Group`.

## Bước 5. Remove Host khỏi vCenter

1. Chuột phải host `172.30.50.52`.
2. Chọn **Remove from Inventory**.
3. Xác nhận **Remove**.
4. Kiểm tra **Recent Tasks**.

Các task cần Completed:
- Remove storage pool disk(s).
- Remove host.
- Update vSAN configuration.

**Kết quả:** Host `.52` không còn trong vCenter Inventory.

## Bước 6. Kiểm tra lại Cluster

Vào:

`Cluster` → `Monitor` → `vSAN` → `Skyline Health` → `RETEST`

Kiểm tra:

- `Cluster chỉ còn 3 host` `.51`, `.53`, `.54`.
- `Resyncing Objects` = 0.
- `Virtual Objects` = Healthy.
- `Storage Policy` = Compliant.
- Các VM hoạt động bình thường.
- `Sphere Cluster Services (vCLS)` hoạt động ổn định.

## Bước 7. Shutdown Server vật lý

Truy cập:

`https://172.30.50.52/ui`

1. Đăng nhập tài khoản `root`.
2. Kiểm tra không còn VM hoặc dịch vụ cần duy trì.
3. Chọn **Host → Actions → Shut Down**.
4. Chờ server tắt hoàn toàn.
5. Rút dây mạng và nguồn nếu cần di dời.

---

## NHỮNG ĐIỂM CẦN ĐẶC BIỆT CHÚ Ý

1. **Không Remove Host trước khi xử lý dữ liệu vSAN.**
2. **Ưu tiên Full data migration** khi loại bỏ host vĩnh viễn.
3. **Resync = 0 chưa đủ chứng minh dữ liệu an toàn**; phải kiểm tra Object Health và Policy Compliance.
4. **Kiểm tra Storage Policy:** Một số cấu hình RAID-5/FTT có thể không còn đáp ứng khi giảm từ 4 xuống 3 host.
5. **Không shutdown server khi task đang chạy.**
6. **Sau khi Remove Host phải chạy lại Health Check** để xác nhận cluster hoạt động ổn định.

**Thứ tự chuẩn:**

`Health Check` → `Data Migration Pre-check` → `Full Data Migration` → `Maintenance Mode` → `Remove vSAN Disks` → `Remove Host` → `Health Check` → `Shutdown`
