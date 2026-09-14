How to Reset Windows Password Without Any Software
-----

## B1: Tại màn hình Login Window Password
- Nhấn giữ phím `Shift`
- Nhấn chọn `Restart`

## B2: Chọn `Continue` -> `Advanced options` -> `Command Prompt` 

## B3: Tại đường dẫn
```
X:\Windows\System32>
```

Chạy các câu lệnh sau:
```
c:
cd windows
cd system32
ren utilman.exe utilman1.exe
ren cmd.exe utilman.exe
```

`Exit CMD` -> chọn `Continue`

## B4: Về màn hình login **(Chọn biểu tượng hình người bên trái cạnh Power)**

Tại đường dẫn:
```
X:\Windows\System32>
```

Nhập:
```
control userpasswords2
```

## B5: Nhấn chọn thay đổi mật khẩu cho tài khoản Aministrator

## B6: Đăng nhập bằng tài khoản và mật khẩu Administrator
