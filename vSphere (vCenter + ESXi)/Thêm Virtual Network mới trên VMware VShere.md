Mục đích tạo thêm 1 Virtual Network (hoặc Port Group) trên ESXI Host để gán cho máy ảo (VM) sử dụng VLAN mới
---

> Áp dụng cho Host sử dụng Standard vSwitch (vSwitch) cho không áp dụng cho vSphere Distributed Switch (vDS)

1/ Kiểm tra VDI hiện tại đang sử dụng Host nào?

2/ Bấm chọn Host có IP đó 

Datacenter -> Cluster -> ESXi Host

(Ví dụ: Host 172.16.2.18)

3/ Mở phần Virtual Switch

Chọn 

Configure -> Networking -> Virtual Switches

Tại đây sẽ thấy : Standard Switch: vSwitch0

> Đây là nơi chứa các Virtual Network hiện có

4/ Thêm Virtual Network

Chọn: ADD NETWORKING

5/ Chọn loại Network

Chọn: Virtual Machine Port Group for a Standard Switch

Sau đó chọn: -> Next

6/ Chọn 

vSwitch0 -> Next

7/ Khai báo Port Group

Ví dụ: Tạo VLAN 282

| Thông số  | Giá trị  |
| --- | --- |
| Network Lable | VLAN-282 |
| VLAN ID       | 282      |

Sau đó chọn: Next -> Finish

8/ Gán Network cho VM là xong

-> Nhớ đánh dấu chọn: 
- Connected
- Connected at power onon
