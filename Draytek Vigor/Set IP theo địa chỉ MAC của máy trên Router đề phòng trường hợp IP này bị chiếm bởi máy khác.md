Hướng dẫn set IP theo địa chỉ MAC của máy trên Router Draytek đề phòng trường hợp IP này bị chiếm bởi máy khác
-------------

1/ Truy cập vào IP Router

2/ Chọn "LAN" => "Bind IP to MAC"

3/ Thêm bản ghi IP–MAC

4/ Ở khung bên phải, các ô đã tự điền:

IP Address: 192.168.1.161 (Chỉ cần nhập IP là có thể thấy MAC luôn)

MAC Address: 00-0C-29-4F-BB-39 (MAC của UniFi Controller)

Comment: gõ một tên để dễ nhớ, ví dụ đặt "serevr unifi controller local".

Nhấn "Add" → bản ghi sẽ xuất hiện ở danh sách IP Bind List bên dưới.

5/ Bật chức năng Bind

Ở trên cùng, chuyển tùy chọn từ Disable sang Enable.

(Tuỳ chọn) Nếu bạn muốn chỉ những máy có trong danh sách mới được cấp IP, có thể tick Strict Bind, nhưng thường không cần.

6/ Lưu cấu hình

Nhấn OK hoặc Apply (tùy firmware, có thể hiện ở cuối trang).

Nếu router hỏi xác nhận, chọn Yes/Save.

7/ Kiểm tra

Vào lại IP Bind List để chắc chắn dòng 192.168.1.161 – 00-0C-29-4F-BB-39 – unifi đã hiển thị.

Trong mục Diagnostics → ARP Table hoặc DHCP Table bạn sẽ thấy thiết bị “unifi” được cấp đúng IP 192.168.1.161.
