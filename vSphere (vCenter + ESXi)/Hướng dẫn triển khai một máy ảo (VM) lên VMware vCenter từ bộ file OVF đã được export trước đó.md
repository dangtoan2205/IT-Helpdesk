HƯỚNG DẪN DEPLOY VM TỪ FILE OVF TRÊN VMWARE VCENTER
----

## 1. Mục đích
Hướng dẫn triển khai một máy ảo (VM) lên VMware vCenter từ bộ file OVF đã được export trước đó.

Bộ file sử dụng trong trường hợp này:

- `Proxy_linux.ovf`: chứa thông tin cấu hình VM.
- `Proxy_linux-disk1.vmdk`: ổ đĩa của VM.
- `Proxy_linux-file1.iso`: file ISO được tham chiếu trong OVF.

VM sau khi triển khai: **Proxy_CA**.

## 2. Deploy OVF Template
Truy cập **vSphere Client** và đăng nhập vCenter.

Tại Cluster/Resource Pool cần triển khai VM:

**Chuột phải Resource Pool** → **Deploy OVF Template**

Ví dụ:

**A05-Cluster → QLVB → Deploy OVF Template**

Tại màn hình **Select an OVF template**:

**1.** Chọn **Local file.**

**2.** Chọn **UPLOAD FILES.**

**3.** Chọn đồng thời các file của VM:
   - `Proxy_linux.ovf`
   - `Proxy_linux-disk1.vmdk`
   - `File .iso nếu OVF yêu cầu`.

**4.** Chọn Next.

> **Lưu ý**: Nếu vCenter báo `1 required file(s) missing`, cần xem tên file được báo thiếu. Ví dụ `Proxy_linux-file1.iso` nghĩa là OVF đang tham chiếu tới file ISO này.

## 3. Cấu hình VM khi Deploy

Thực hiện lần lượt các bước trong wizard:

- **Select a name and folder**: Đặt tên VM, ví dụ Proxy_CA.
- **Select a compute resource**: Chọn Cluster/Host hoặc Resource Pool phù hợp.
- **Select storage**: Chọn Datastore đủ dung lượng. Có thể sử dụng Thin Provision để tối ưu dung lượng lưu trữ nếu phù hợp với hệ thống.
- **Select networks**: Map card mạng của VM sang đúng Port Group/VLAN. Trong trường hợp triển khai hiện tại:

**Network Adapter 1 → DPortGroup-305**

Kiểm tra lại toàn bộ cấu hình và chọn:

**Finish**

Theo dõi:

**Recent Tasks → Deploy OVF template**

Chờ trạng thái:

**Completed**

## 4. Khởi động VM
Sau khi deploy thành công:

**Chuột phải VM → Power → Power On**

Kiểm tra tại **Summary**:
- Power Status: `Powered On`
- VMware Tools: `Running` nếu hệ điều hành và VMware Tools hoạt động bình thường.

## 5. Ngắt CD/DVD sau khi Deploy
Nếu OVF được export khi VM cũ đang gắn ISO, VM mới có thể tiếp tục giữ `CD/DVD Drive` kết nối với ISO.

Khi cố ngắt lúc VM đang chạy có thể xuất hiện lỗi:

> **`Connection control operation failed for disk 'ide1:0'`**

Hoặc cảnh báo:

> **`The guest operating system has locked the CD-ROM door...`**

Trong trường hợp này, **không Power Off cưỡng bức VM.**

Thực hiện:

**Actions → Power → Shut Down Guest OS**

Chờ:

**Power Status → Powered Off**

Sau đó vào:

**Actions → Edit Settings → Virtual Hardware → CD/DVD drive 1**
Bỏ chọn:
- `Connected`
- `Connect At Power On`
Chọn **OK.**

Theo dõi **Recent Tasks** cho đến khi:

**Reconfigure virtual machine → Completed**

Lúc này CD/DVD đã được ngắt thành công.

## 6. Khởi động lại và kiểm tra
Chọn:

**Power → Power On**

Sau khi VM khởi động, kiểm tra:
- VM boot bình thường từ Hard Disk.
- VMware Tools hoạt động.
- Network Adapter ở trạng thái Connected.
- Đúng Port Group/VLAN.
- IP, subnet mask, gateway và DNS đúng.
- Không trùng IP với VM cũ.
- Các service trên VM hoạt động bình thường.

