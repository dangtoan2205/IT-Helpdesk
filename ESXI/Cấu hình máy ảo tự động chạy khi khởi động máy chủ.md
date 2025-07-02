![image](https://github.com/user-attachments/assets/aff50df7-b10c-452d-a80c-628d5414a0f8)

Với các máy chủ cài đặt Proxmox và ESXi đang dùng ở nhà, mình đều kích hoạt tính năng tự động chạy mỗi khi được cấp nguồn (Power on when AC detected) trong BIOS. Nghĩa là nếu trong trường hợp bị cúp điện, máy chủ sẽ mở lên lại ngay khi có điện, không cần mình phải thao tác mở máy thủ công bằng tay.

Tuy nhiên, với thiết lập mặc định, các máy ảo trong của ESXi sẽ không tự động chạy mỗi khi khởi động lại máy chủ. Việc này gây phiền toái trong quá trình sử dụng.

Để xử lý vấn đề này, mình đã phải Google cách cấu hình máy ảo tự động chạy trên ESXi 7.0. Và dưới đây là hướng dẫn nhanh gọn cách làm:

Truy cập vào Web Client của ESXi, thao tác lần lượt như sau

**1. Bấm vào Manage ở phần Navigator bên trái**

**2. Chọn System**

**3. Chọn Autostart**

**4. Bấm Edit settings**

![sleekshot](https://github.com/user-attachments/assets/cf847de4-ee60-4caf-a44a-ffb86e44a610)


Cửa sổ `Change autostart configuration` sẽ hiện ra. Chọn `Yes` ở mục `Enabed` và bấm `Save` để lưu lại.

![image](https://github.com/user-attachments/assets/fad287e2-d896-4cbc-9e39-dba309f13bdd)

Tiếp theo, chọn máy ảo bạn muốn tự động chạy mỗi khi máy chủ khởi động. Sau đó bấm vào nút `Enable` để kích hoạt.

![image](https://github.com/user-attachments/assets/b23b37bf-039c-4ced-b7be-3c264971ae42)

Bạn có thể bấm tiếp vào nút Start ealier hay Start later để thay đổi thứ tự khởi động của các máy ảo trong cùng hệ thống. Thứ tự được hiển thị trong cột Autostart order
---------

Chẳng hạn như hình minh họa bên dưới, thứ tự khởi động của các máy ảo như sau:

1. pfSense
2. Ubuntu
3. Alpine Linux
4. focal

![image](https://github.com/user-attachments/assets/81e7d952-fe0c-4a0f-aef6-e761e9b37107)

Bạn còn có thể thay đổi thời gian chờ giữa các lần khởi động máy ảo nếu muốn (mặc định đang là 120 giây).



