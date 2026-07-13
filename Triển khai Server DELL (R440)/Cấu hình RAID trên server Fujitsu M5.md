Cấu hình RAID và boot cài hệ điều hành trên server Fujitsu M5
-----

1/ Khởi động nguồn server và nhấn phím F2 để vào màn hình Setup


2/ Trên màn hình Setup -> chọn tab Advanced -> chọn tiếp -> SATA Configuration


3/ Tại màn hình SATA Configuration

SATA Mode hiện tại đang là [AHCI Mode] chuyển thành [RAID Mode] -> bấm Esc để thoát ra


4/ Sau khi thoát ra tab Advanced -> chọn AVAGO MegaRAID <PRAID CP400i> Configuration Utility - 03.25.05.10 


5/ Chọn Main Menu (để vào màn hình chế độ RAID)


6/ Tiến hành xóa RAID đang có 

Chọn Configuration Management -> chọn Clear Configuration 

Tại màn hình này nhấn Enter để chuyển Confirm từ [Disabled] sang [Enabled] 

Sau khi chuyển sang [Enable] thì chọn Yes -> chọn OK lần nữa để xác nhận xóa các RAID hiện tại


7/ Sau đó chọn Virtual Drive Management (để kiểm tra đã xóa RAID thành công chưa)


Như hình ảnh là đã xóa RAID cũ thành công


8/ Tiến hành tạo lại RAID mới. Chọn Configuration Management -> chọn Create Virtual Drive

- Select RAID Level (Chọn RAID mong muốn) (Trong trường hợp này là cài 1 os ubuntu server mà trên server đang chỉ có 2 ổ cứng 300Gb -> thì chọn RAID 1)

- Select Drives (Chọn ổ cứng mong muốn gộp thành RAID 1) (Nếu chọn hết thì chọn Check All) -> chọn Apply Changes -> chọn OK (để xác nhận)

- Sau đó chọn Save Configuration -> Confirm (chuyển Disabled -> Enabled) -> chọn Yes -> chọn OK (để xác nhận tạo RAID).


 9/ Kiểm tra xem đã tạo RAID thành công hay chưa

 Chọn Virtual Drive Management (Như hình ảnh là đã được tạo RAID1 thành công)



 10/ Chế độ SATA Mode hiện tại đang là [RAID Mode] cần chuyển thành [AHCI Mode] để có thể boot OS từ USB

 Bấm Esc để thoát về màn hình tab Advanced -> Chọn SATA Mode -> chuyển [RAID Mode] thành [AHCI Mode]


 11/ Bấm Esc để thoát và chuyển sang tab Save & Exit -> chọn Save Changes and Exit (để lưu cấu hình và thoát khỏi màn hình Enter Setup)


 12/ Khi server khởi động lại chọn phím F12 để vào Menu boot -> Chọn boot bằng USB -> Cài os như bình thường.
