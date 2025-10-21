Chuẩn bị nhanh

- Biết User/Password của đầu ghi (admin).
- Đầu ghi online cùng mạng (hoặc đã gán Hik-Connect nếu muốn xem từ xa).
- Cổng thường dùng: SDK 8000, HTTP 80/88, RTSP 554 (giữ mặc định là được nếu không NAT).

---------

1, Đăng nhập với tài khoản và mật khẩu của Hik-Vision tự tạo vào iVMS-4200
-----

2, Mở iVMS-4200 → vào Device Management (bên phải).
-----

<img width="1912" height="1026" alt="image" src="https://github.com/user-attachments/assets/ac442aef-764f-4cea-a7f7-b6204dceb75b" />

3, Tab Device → bấm Add.
-----

<img width="1917" height="540" alt="image" src="https://github.com/user-attachments/assets/98b10396-e9eb-4420-a328-d41fd5ba613c" />

4, Chọn Adding Mode: IP/Domain.
-----

Address: gõ IP của đầu ghi (ví dụ 192.168.80.15 – đây là IP NVR, không phải IP từng camera).

Port: 8000 (SDK).

Username/Password: tài khoản đầu ghi.


<img width="564" height="704" alt="image" src="https://github.com/user-attachments/assets/9bcb9202-1044-454a-9478-0647e3907df2" />

5, Bấm Add → trạng thái chuyển Online.
-----

<img width="1914" height="508" alt="image" src="https://github.com/user-attachments/assets/93701258-55dd-4315-9279-14e49b4202a0" />

6, Vẫn trong Device Management, chọn đầu ghi vừa thêm 
→ Remote Configuration (hoặc Import Camera/Channel nếu có nút này).
iVMS sẽ tự kéo toàn bộ kênh (CH1…CHn).
-----

<img width="1919" height="523" alt="image" src="https://github.com/user-attachments/assets/816119ef-f5ef-4259-b70e-adf37f7ef8c1" />

<img width="1914" height="500" alt="image" src="https://github.com/user-attachments/assets/16babd29-8d79-4fbc-83b2-34c55fc99a03" />

Chọn `Basic Settings`

<img width="893" height="793" alt="image" src="https://github.com/user-attachments/assets/fff3d44d-ce68-4ae3-9abd-c9ae0eefdfeb" />

Chọn `Save`

7, Vào Main View để xem trực tiếp. Bạn có thể:
-----

- Nhấp phải kênh → Stream Type chọn Substream khi xem nhiều kênh, Mainstream khi phóng to.
- Create View để lưu layout 4/9/16 kênh yêu thích.

<img width="1915" height="760" alt="image" src="https://github.com/user-attachments/assets/278dd75b-60bc-4899-b5c0-428a16142a43" />

<img width="1913" height="1023" alt="image" src="https://github.com/user-attachments/assets/f92edd42-489b-4d58-a3cd-f6f982e0ea00" />

-----
Lưu ý: nếu trước đây bạn add từng camera rời vào iVMS, hãy Remove chúng để tránh trùng. Chỉ giữ đầu ghi – các kênh sẽ đi theo đầu ghi.
-----


